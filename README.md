
# 🚰 Refill Application System

This project controls a liquid refill system involving a **Supply Tank** and two **Process Tanks**. It utilizes a series of pumps, valves, and sensors to ensure smooth operation and fault handling. Below is an overview of how the system works.

---

## 🛠️ **System Components**

* **Supply Tank** 💧
* **Process Tanks (2 tanks)** 🛢️
* **Pumps** 🔄
* **Valves** 🚪
* **Level Sensors** 📏
* **State Handlers** ⚙️

---

## ⚙️ **Program Flow**

The program operates using a **Program Cycle OB** that runs cyclically and triggers the main control logic. Other function blocks (FBs) are called for managing machine states and equipment operations.

---

## 🔄 **Machine State Handler (FB)**

The **Machine State Handler** manages the overall state of the system. There are 4 key states:

1. **Ready (State 0)** ✅

   * System is idle, waiting to start.
   * 🌟 **Ready Lamp** is on.

2. **Running (State 1)** ▶️

   * System is actively refilling.
   * 🌟 **Running Lamp** is on.

3. **Faulted (State 2)** ⚠️

   * A fault has occurred.
   * 🌟 **Fault Lamp** is on.

4. **Reset (State 3)** 🔄

   * System is resetting.
   * ⏱️ Waits 2 seconds before returning to **Ready** state.

### 🔁 **State Transitions:**

* **Ready (0)** → **Running (1)** when **Start** is pressed (hardware/software).
* **Running (1)** → **Ready (0)** when **Stop** is pressed.
* **Ready (0)** or **Running (1)** → **Faulted (2)** if any fault occurs.
* **Faulted (2)** → **Reset (3)** when **Reset** is pressed.
* System goes back to **Ready (0)** after 2 seconds in **Reset**.

---

## 💧 **Supply Module (FB)**

The **Supply Module** controls the **Supply Tank**, which has a low-level sensor and a valve for filling process tanks.

### ⚙️ **Valve Control:**

* Valve opens only when:

  * System is **Running** 🏃‍♂️
  * Supply tank is **not** at low level 📉

### ⚠️ **Faults:**

* **Low Level Fault** 🚨 (Triggerword bit 0)
* **Valve Open Position Fault** 🚨 (Triggerword bit 1)

### 🔄 **Fault Reset:**

* Faults are cleared when system enters **Reset** state. All faults reset by setting triggerword bits to `0`.

---

## 🛢️ **Refill Module for Process Tanks (FB)**

The **Refill Module** manages each **Process Tank**. Each module consists of an **Analog Level Sensor**, **Pump**, and **Valve**.

### 🔄 **Refill Logic:**

* **Start Refill** when tank level < **10%** 📉
* **Stop Refill** when tank level > **60%** 📈
* **Pump Operation** starts if:

  * System is **Running** 🏃‍♂️
  * Valve is **open** 🚪
  * Supply Tank valve is **open** 🚪

### ⚠️ **Faults:**

* **Motor Circuit Breaker Fault** ⚠️ (Triggerword bit 0)
* **Local Isolator Fault** ⚠️ (Triggerword bit 1)
* **Pump Feedback Fault** ⚠️ (Triggerword bit 2)
* **Valve Position Fault** ⚠️ (Triggerword bit 3)

### 🔄 **Fault Reset:**

* All faults reset when system enters **Reset** state.

---

## 📊 **Data Structures & Outputs**

These data structures store real-time status information of the system components:

### **Supply Data Structure** 💧

* **Low Level Status** 📉
* **Valve State** 🚪

  * `0` = Closed
  * `1` = Open
  * `2` = Faulted
* **Valve Position Status** 🚪
* **Fault Summary** ⚠️

### **Refill Data Structure** 🔄 (for each Process Tank)

* **Tank Water Level** 💧
* **Pump State** 🔌

  * `0` = Off
  * `1` = Running
  * `2` = Faulted
* **Pump Running Status** 🔄
* **Valve State** 🚪

  * `0` = Closed
  * `1` = Open
  * `2` = Faulted
* **Valve Position Status** 🚪
* **Fault Summary** ⚠️

---

## 🛑 **Fault Handling & Reset Logic**

* **Fault Handling:** All faults are tracked and updated in real-time.
* **Reset Logic:** Pressing the **Reset** button clears all faults and transitions the system back to the **Ready** state after a 2-second delay.

---

## 📈 **Summary**

This refill application is designed to manage the refilling of two process tanks from a supply tank. It includes:

* **State management** for controlling the system's operation modes (Ready, Running, Faulted, and Reset).
* **Fault detection** to ensure the system operates reliably.
* **Automated refilling** based on tank levels, ensuring smooth operation with safety checks.

