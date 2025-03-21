## 📌 Implemented Features
 
### 🔧 **1. Gear System with Sound**
- Multiple **gears** affecting speed and acceleration.
- Gear shifts are accompanied by **different engine sounds**.
 
**Technical Details:**
- `Rigidbody` is used for truck movement.
- Script to **simulate a manual/automatic gearbox**.
- Sound effects for **RPM-based engine noises**.
 
---
 
### ⛽ **2. Gas Station System**
- The truck consumes **fuel**, depending on speed and distance traveled.
- Gas stations allow **refueling**, and the UI displays the current fuel level.
- If the tank is empty, the truck **cannot drive anymore**.
 
---
 
### 🖥 **3. GUI for Speedometer & Gear Display**
- Speed in **km/h** is displayed in a UI gauge.
- The current gear is displayed in a **separate UI element**.
- Implemented using Unity’s **Canvas UI** and `TextMeshPro`.
 
**Technical Details:**
- `Rigidbody.velocity.magnitude` calculates speed.
- UI updates dynamically using `TextMeshProUGUI`.
 
---
 
### 🎮 **4. Occlusion Culling (Performance Optimization)**
- Only **visible objects** are rendered to **save performance**.
- Unity’s **Occlusion Culling System** is enabled.
- Prevents unnecessary rendering of **objects hidden behind buildings, mountains, etc.**.
 
**Technical Details:**
- `Occlusion Culling` enabled in **Lighting & Rendering Settings**.
- `Occlusion Areas` placed for better control.
 
---
 
## 📌 Future Improvements & To-Do
- 🛞 **Better trailer system** (Configurable Joint for more stable connection).
- 🛠 **Improved driving physics** (weight adjustments, realistic suspension system).
 
---
 
## 🔗 Resources & References
- Unity Docs: [https://docs.unity3d.com/](https://docs.unity3d.com/)
- Vehicle physics tutorials: [https://www.youtube.com/c/Brackeys](https://www.youtube.com/c/Brackeys)
