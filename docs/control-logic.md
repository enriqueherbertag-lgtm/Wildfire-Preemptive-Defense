# Wildfire Preemptive Defense (WPD) – Lógica de control (Anteproyecto)

---

## 1. Modos de operación

| Modo | Descripción | Activación |
|------|-------------|------------|
| **Local autónomo** | La estación decide sola (detección → verificar agua → activar aspersores). | Por defecto. |
| **Coordinado central** | El servidor central recibe alertas y ordena activaciones múltiples. | Cuando hay comunicación con el servidor. |
| **Manual** | Un operador activa/desactiva la estación (por LoRa o interfaz local). | Por comando externo. |

---

## 2. Ciclo local (estación)

**Pasos:**

1. **Sensor perimetral** detecta amenaza (temperatura, humo, gases) durante más de 5 segundos.
2. **Alarma local** se activa.
3. **Verifica nivel de agua**:
   - Si el estanque está vacío (menos del 10% de nivel) → activa bomba de llenado.
4. **Bomba de llenado** funciona hasta que el **presostato** indica "estanque lleno" (tiempo máximo: 1 minuto).
5. **Si el estanque está lleno** → activa bomba de presión y aspersores en el sector afectado.
6. **Mantiene la barrera** de agua mientras persista la alerta.
7. **Si la alerta cesa** (más de 30 segundos sin detección) → desactiva aspersores y bomba de presión.

---

## 3. Reglas de activación

| Condición | Acción local | Acción hacia el servidor |
|-----------|--------------|--------------------------|
| Detección confirmada (sensor > umbral durante > 5 seg) | Alarma local (LED, zumbador) | Enviar evento "Detección sector X". |
| Estanque vacío (nivel < 10%) | Activar bomba de llenado. | Enviar "Alerta agua baja". |
| Estanque lleno (presostato activo) | Desactivar bomba de llenado. | Enviar "Estanque OK". |
| Fuego activo (alarma + estanque lleno) | Activar bomba de presión + aspersores. | Enviar "Defensa activa sector X". |
| Alarma desactivada (sensores en normal durante > 30 seg) | Desactivar aspersores (bomba presión OFF). | Enviar "Sector X normal". |

---

## 4. Temporizadores

| Temporizador | Valor por defecto | Ajustable por |
|--------------|-------------------|----------------|
| Confirmación de detección | 5 segundos | Servidor central |
| Tiempo máximo de llenado | 1 minuto | Servidor central |
| Persistencia de aspersores | Hasta fin de alerta (mínimo 30 seg) | Servidor central |
| Silencio para desactivación | 30 segundos sin detección | Servidor central |

---

## 5. Comunicación con el servidor central ("Director de Emergencias")

### Mensajes (de la estación al servidor)

| Mensaje | Formato | Ejemplo |
|---------|---------|---------|
| Alarma detectada | `ALARM,ID_estacion,SECTOR` | `ALARM,03,NORTE` |
| Nivel de agua bajo | `WATER_LOW,ID_estacion,XX%` | `WATER_LOW,03,8%` |
| Estanque lleno | `WATER_OK,ID_estacion` | `WATER_OK,03` |
| Defensa activada | `DEFENSE_ON,ID_estacion,SECTOR` | `DEFENSE_ON,03,NORTE` |
| Defensa desactivada | `DEFENSE_OFF,ID_estacion,SECTOR` | `DEFENSE_OFF,03,NORTE` |
| Estado general (heartbeat) | `STATUS,ID_estacion,BAT%,WATER%,TEMP` | `STATUS,03,85%,98%,32°C` |

### Comandos (del servidor a la estación)

| Comando | Efecto |
|---------|--------|
| `ACTIVATE,SECTOR` | Activa aspersores en ese sector (aunque no haya alerta local). |
| `DEACTIVATE,SECTOR` | Desactiva aspersores. |
| `FILL_ON` / `FILL_OFF` | Control manual de bomba de llenado. |
| `SET_THRESHOLD,XX` | Cambia umbral de detección (temperatura, etc.). |
| `REBOOT` | Reinicia el PLC de la estación. |

---

## 6. Lógica del servidor central

**Flujo de procesamiento (textual):**

1. El servidor recibe un mensaje de una estación.
2. Si el mensaje es `ALARM`:
   - Registra el evento en una bitácora.
   - Activa una alerta visual en el mapa (sector afectado).
   - Verifica si hay otras estaciones en el mismo sector.
     - Si las hay, coordina una activación conjunta.
     - Si no, espera confirmación o el fin de la alerta.
3. Si el mensaje es `WATER_LOW` (agua baja):
   - Registra una alerta de recurso (requiere atención).
4. Si el mensaje es `DEFENSE_ON`:
   - Marca el sector como "defensa activa" en el mapa.
5. En cualquier otro caso, actualiza el estado general de la estación.

---

## 7. Ejemplo de operación

1. **Sensores** en estación 03 (sector NORTE) detectan temperatura anómala.
2. **PLC local** espera 5 segundos (confirmación).  
3. **Alarma** local se activa.  
4. **Verifica** nivel de agua: 8% (bajo).  
5. **Activa** bomba de llenado durante 1 minuto.  
6. **Presostato** indica "estanque lleno". Bomba de llenado se desactiva.  
7. **Activa** bomba de presión y aspersores en sector NORTE.  
8. **Envía** a servidor central: `DEFENSE_ON,03,NORTE`.  
9. **Servidor** marca el sector en rojo y activa alarma general.  
10. **Sensores** indican normalidad durante 30 segundos.  
11. **Estación** desactiva aspersores y envía `DEFENSE_OFF,03,NORTE`.  
12. **Servidor** actualiza el mapa.

---

## 8. Notas finales

- Todos los temporizadores y umbrales son **configurables** desde el servidor central.
- La comunicación entre estaciones y servidor es **bidireccional** y usa **LoRa** (o el medio disponible).
- En ausencia de servidor, las estaciones operan en **modo local autónomo**.
- Este documento es parte del anteproyecto de WPD y complementa el `README.md` principal.
