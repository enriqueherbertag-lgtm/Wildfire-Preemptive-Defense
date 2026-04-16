# Wildfire Preemptive Defense (WPD) – Preliminary Design

**Automated perimeter system for proactive defense against forest and urban wildfires.**

---

## 1. Problem statement

Current wildfire response is **reactive**: firefighters, aircraft, helicopters. By the time they arrive, the fire has already advanced. Communities, industries, and forests remain exposed.

**WPD changes the paradigm:** it is a **proactive defense**, based on fixed and autonomous infrastructure.

---

## 2. Proposed solution

A **network of perimeter stations** that detect fire before it reaches the critical area and automatically activate a **water barrier**.

### Components per station

| Component | Specification |
|-----------|---------------|
| **Focused well** | Local water supply. |
| **Reinforced concrete tank** | Buried or semi-buried. |
| **Concrete enclosure** | Protects pumps and controls. |
| **Tower** | Steel or concrete, height according to coverage. |
| **Sprinkler** | At the top of the tower. Coverage: 50 m to each side (overlap). |
| **Fill pump** | Fills the tank from the well. |
| **Pressure pump** | Feeds the sprinkler network. |
| **Pressure switch** | Detects "tank full" and stops the fill pump. |
| **Perimeter sensors** | Temperature, smoke, gases. Located 300 m from the tower, facing the threat. |
| **PLC / industrial controller** | For local control logic. |
| **Solar panel + deep-cycle batteries** | Autonomous energy. Charge controller. |
| **LoRa transceiver** | Communication between stations and central server. |
| **Central server (optional)** | Emergency Director in the absence of humans. |

---

## 3. Operation logic

### Modes

| Mode | Description |
|------|-------------|
| **Local autonomous** | The station decides alone (detection → check water → activate sprinklers). |
| **Centrally coordinated** | The central server receives alerts and orders multiple activations. |
| **Manual** | An operator activates/deactivates the station via LoRa or local interface. |

### Local cycle

1. **Perimeter sensor** detects threat (temperature, smoke, gases) for >5 seconds.
2. **Local alarm** is activated.
3. **Water level check**:
   - If **tank empty** (<10%) → activates fill pump until pressure switch (max. 1 minute).
4. **If tank is full** → activates pressure pump and sprinklers in the affected sector.
5. **Maintains** the water barrier as long as the alert persists.
6. **If alert ceases** (>30 seconds without detection) → deactivates sprinklers.

### Communications

| Origin | Destination | Medium | Content |
|--------|-------------|-------|---------|
| Station | Central server | LoRa | Alarms, water level, status, activations. |
| Central server | Station | LoRa | Remote activation, threshold changes, reboot. |
| Central server | Operator | LoRa, SMS, app | Notifications, sector map. |

---

## 4. Energy

| Component | Specification |
|------------|---------------|
| **Generation** | Solar panel (parallel string per station). |
| **Storage** | Deep-cycle batteries (autonomy to be calculated: pump consumption, sensors, LoRa). |
| **Control** | Charge controller + PLC for energy management. |

---

## 5. Water

| Component | Specification |
|------------|---------------|
| **Source** | Focused well per station. |
| **Storage** | Reinforced concrete tank, buried. |
| **Filling** | Fill pump (activated by low level). |
| **Pressurization** | Pressure pump (activated by alert + tank full). |
| **Level control** | Pressure switch (stops filling when tank is full). |

---

## 6. Detection and coverage

| Parameter | Value |
|-----------|-------|
| **Distance between towers** | 100 meters. |
| **Sprinkler coverage** | Must cover at least 50 meters to each side (overlap). |
| **Sensor location** | 300 meters from the tower, facing the threat front. |
| **Sensor types** | Temperature, smoke, gases (models to be defined). |

---

## 7. Estimated costs (per station)

| Component | Cost (USD) |
|-----------|-------------|
| Well and concrete tank | 2,000 - 5,000 |
| Fill and pressure pumps | 500 - 1,000 |
| Tower, sprinkler, piping | 1,000 - 2,000 |
| Solar panel + batteries + charge controller | 1,000 - 2,000 |
| PLC / controller + sensors + LoRa | 500 - 1,000 |
| **Approximate total** | **5,000 - 11,000 USD** |

*In volume (>100 stations), costs can be significantly reduced.*

---

## 8. Project status

| Item | Status |
|------|--------|
| Concept | ✅ Defined |
| Technical specifications | ✅ Complete (this document) |
| Control logic | ✅ Defined |
| Components selected | ✅ Preliminary |
| Mechanical drawings | 🔲 Pending |
| Electrical schematics | 🔲 Pending |
| PLC firmware | 🔲 Pending |
| Central server software | 🔲 Pending |
| Field prototype | 🔲 Pending |
| Real fire validation | 🔲 Pending |

---

## 9. Citation

If you use WPD in your research or project, please cite:

Enrique Aguayo H. (2026). Wildfire Preemptive Defense (WPD): Preliminary design for automated perimeter system (v1.0). Zenodo. https://doi.org/10.5281/zenodo.xxxxxx

---

## 10. Author

**Enrique Aguayo H.** – Independent Researcher  
Contact: eaguayo@migst.cl  
ORCID: 0009-0004-4615-6825  
GitHub: @enriqueherbertag-lgtm  

*Documentation assisted by DeepSeek (AI).*

---

## 11. License

Copyright © 2026 Enrique Aguayo. All rights reserved.

**Permitted:** Non-commercial use for educational or research purposes. Unmodified distribution with attribution.

**Prohibited without express permission:** Commercial use, modification for production environments, distribution of modified versions.

For commercial licenses: eaguayo@migst.cl
