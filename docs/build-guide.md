# The "Tall-Boy" Hybrid Build Guide
### *Step-by-Step Implementation of the Stability-Optimized Architecture*

This guide integrates the standard MPCNC Primo assembly process with the specific "Tall-Boy" modifications derived from our engineering analysis.

---

## Phase 1: The Printed Parts
*Utilizing the MPCNC Primo geometry with specific material optimizations.*

**1. Sourcing Files:**
*   Download the official **MPCNC Primo** files from the V1 Engineering GitHub or Printables.
*   **Critical Selection:** Download the **"Version J" (25.4mm)** files. This size is mandatory for the 1" tubing required to achieve the necessary stiffness.

**2. Printing Strategy:**
*   **Core & Trucks (The Structure):** Print in **PLA+** for maximum stiffness.
    *   *Settings:* 3+ Perimeters, 45%+ Infill. Print the Core with variable infill (70% in stress areas) or solid walls.
    *   *Why:* PLA resists deformation under load better than PETG, which is critical for maintaining bearing alignment.
*   **The "Roof" Brace (The Portal Frame):** Print this custom part in **PETG** or **ABS/ASA**.
    *   *Why:* This part connects the top of the Z-tower. It needs to withstand potential heat rising from the motors and absorb vibration without snapping.
*   **Corners & Legs:** Standard PLA/PETG is acceptable.

**3. Parts List (Summary):**
*   1x Core Assembly (The "Junction").
*   4x Trucks (The "Rollers").
*   4x Corner Assemblies.
*   1x Z-Axis Motor Mount (Extended/Modified for "Roof").
*   1x Custom "Roof" Brace (Connects Z-pipes at the top).

---

## Phase 2: The Metal "Bones" (Hybrid Material Strategy)
*This is the most critical hardware change. We split materials based on physics: Light Moving Parts vs. Heavy Anchors.*

**1. Material Selection:**
*   **Moving Pipes (X, Y, Z):** **1" (25.4mm) OD Thin-Wall Stainless Steel Tubing.**
    *   *Spec:* Wall thickness **0.049" - 0.065"** (1.25mm - 1.65mm).
    *   *Constraint:* Do NOT use thick-wall DOM here. The weight penalty increases the "lean" torque by ~25% and kills acceleration.
    *   *Finish:* Request "Seamless" or "Drawn" for best straightness.
*   **Stationary Legs (Frame):** **1" (25.4mm) DOM Steel** or **Black Iron Pipe**.
    *   *Spec:* Thick wall (Schedule 40 or DOM 0.095").
    *   *Why:* Heavy, cheap, rigid. This anchors the machine to the table.

**2. Cut List (Standard Desktop ~12" Work Area):**
*   **X-Axis Gantry (2x):** 24" (600mm) - *Stainless Steel*.
*   **Y-Axis Gantry (2x):** 24" (600mm) - *Stainless Steel*.
*   **Z-Axis Rails (2x):** **24" (600mm)** - *Stainless Steel*. (**The "Tall-Boy" Mod**: Standard is ~12". You are doubling this).
*   **Frame Legs (4x):** Calculate length based on your table thickness + 2" clearance. - *DOM/Black Pipe*.

---

## Phase 3: Assembly (Stability Focus)
*Follow standard V1 Primo instructions except where noted below.*

**1. Core & Truck Assembly:**
*   Assemble the Core and Trucks.
*   Install the 608ZZ bearings.
*   *Check:* Ensure the bearings spin freely on your Stainless tubing. The thin-wall SS should still be within the clamping range of the printed parts (Version J).

**2. The Z-Axis "Portal Frame" (Critical Step):**
This step converts the Z-axis from a wobbly lever into a rigid box.
*   **Step A:** Slide the Z-Motor Mount and Z-Top Plate onto your **Long Z-Pipes** (24").
*   **Step B:** Install the extended T8 Lead Screw (300mm-400mm length).
*   **Step C (The Roof):** Install the **"Roof" Brace**.
    *   *Method:* Slide a 3D-printed rigid spacer/block onto the Z-pipes just below the Motor Mount, or ensure the Motor Mount itself is printed as a solid bridge connecting both pipes.
    *   *Action:* Tighten the clamps on the Motor Mount and the Brace until the Z-pipes are locked parallel. They can no longer bow or twist independently.

**3. Frame Assembly:**
*   Use the heavy **DOM/Black Pipe** legs for the base.
*   Bolt the corners to a thick sheet of MDF or a rigid table.
*   *Why:* The lightweight gantry needs a heavy, anchored base to push against. A rigid base prevents the machine from rocking during high-speed 3D printing moves.

**4. The Tool Mount (The Hybrid Swap):**
*   You will not use the standard Makita/DeWalt mounts.
*   **Source:** Search Thingiverse/Printables for "MPCNC Primo Brushless Spindle Mount" or "ER11 Mount 52mm".
*   **Install:** Mount the **Brushless Spindle (3542)** directly to the Z-Axis tool plate.
*   *Future Proof:* If you want to swap to 3D printing later, design a "Dovetail" adapter plate that allows you to slide the spindle out and a Bowden hotend in.

---

## Phase 4: Electronics (The Hybrid Brain)
*Managing the dual nature of the machine.*

**1. The Controller:**
*   **Board:** BTT SKR Pro 1.2, SKR Pico, or Manta (Must support 5+ steppers).
*   **Power:** **24V Power Supply** (Mandatory for stepper torque and heating speed).

**2. Wiring Logic:**
*   **Motors:** Wire X, Y1, Y2, Z, and Extruder motors.
*   **Spindle Control:**
    *   Connect the Brushless Motor to the **ESC (Electronic Speed Controller)**.
    *   Connect the ESC Signal Wire to a **FAN** or **SERVO** header on the controller board.
    *   *Result:* The firmware can vary spindle RPM via G-code (M3/M4 commands).
*   **Hotend:** Connect Heater + Thermistor to the `E0` header.
*   **Wiring Drag:** Use a **CAN Bus Toolhead Board** (e.g., BTT EBB36) if possible. This reduces the cable bundle weight significantly, helping acceleration.

---

## Phase 5: Software (The Digital Stabilizer)
*Software is as critical as hardware for the "Tall-Boy".*

**1. Firmware:**
*   **Klipper** is highly recommended.
*   **Input Shaping:** Use an accelerometer (like an ADXL345) to measure the resonance of your lightweight gantry.
    *   *Why:* The Tall-Boy is light enough to accelerate fast, but that causes ringing. Input Shaping mathematically cancels this, giving you "Heavy Machine" quality at "Light Machine" speeds.

**2. Profiles:**
*   **CNC Mode:** Disable `heater_pin` and `sensor_pin` safety checks. Map E-stepper to Spindle RPM.
*   **Print Mode:** Enable heaters and extruder. Calibrate `rotation_distance` for your Bowden setup.

**3. The Cutting Strategy (HSM):**
*   To avoid "rubbing" (heat) while keeping forces low: Maintain high **Feed Rate** to ensure chips fly and cool the bit. Reduce **Depth of Cut** to 0.3mm-0.5mm to keep lateral force within the 4N-8N safe zone.

---

## Phase 6: The Bed (Simple Table)
*Skipping the "Drop Table" complexity.*

**1. The Base:**
*   Take a flat piece of MDF or Plywood (approx 20" x 20").
*   Mount the MPCNC Corners to the corners of this board.

**2. The Surface:**
*   **CNC Mode:** The MDF board is your spoil surface. You will cut into it slightly.
*   **3D Printing Mode:** Clip a heated bed (with a magnetic PEI sheet) on top of the MDF board. Use simple printed clamps to hold it down.

---

## Summary Checklist for Sourcing

**Structural:**
*   [ ] **Printed Parts:** MPCNC Primo Version J.
*   [ ] **Moving Pipes:** 1" Thin-Wall Stainless Steel (0.049-0.065").
*   [ ] **Legs:** 1" DOM or Black Pipe.
*   [ ] **Lead Screw:** T8 (Extended length 300mm+).

**Electronics:**
*   [ ] **Controller:** BTT SKR Pro / Pico.
*   [ ] **Motors:** 5x NEMA 17.
*   [ ] **Spindle:** 3542 Brushless + 30A ESC.

**Hybrid:**
*   [ ] **Print Head:** V6-Clone + Bowden Extruder.
*   [ ] **Mount:** Custom adapter for Spindle/Hotend.

**You are not inventing a new machine; you are "stretching" the legs and changing the "hat" (the tool).** Follow the standard Primo assembly videos on the V1 website, and pause at the Z-axis assembly to install your longer pipes and the "Roof" brace. This configuration is the optimal path for scaling stability.
