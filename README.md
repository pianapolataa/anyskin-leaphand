# AnySkin - Leap Hand: Magnetic Interference Compensation

This work implements a neural network-based solution for magnetic interference between **AnySkin** tactile sensors when integrated on a **Leap Hand**, specifically when fingers are brought close to each other. This model predicts these interference readings based on finger proprioception data and subtracts them from the raw readings to ensure that sensor output remains close to 0 at positions of no contact.

---
## Features

### 1. Data Collection (`pbcreate_data.py`)
To collect magnetic interference data without physical contact, we generate random, non-colliding trajectories in simulation using Leap Hand's URDf and PyBullet.
* **Joint Range Limiting:** We define a "safe" subspace within the **16-DOF** joint space to minimize self-collision.
* **Filtering Logic:** To ensure data purity, we calculate the magnitude delta between consecutive frames. If the reading jumps beyond a threshold (e.g., **350**), the sample is flagged as a collision and discarded.
* **Normalization:** Raw sensor readings are transformed using a **logarithmic scale** to compress the high range of interference readings.

### 2. MLP Training (`pb_nn.py`)
We utilize a Multi-Layer Perceptron (MLP) to learn the mapping between hand posture and sensor noise.
* **Input (16-DOF):** Joint angles of the 16-DOF Leap Hand.
* **Output (60-dimensional):** Predicted magnetic readings for all sensors.
    * *Note: 4 fingers × 5 Anyskin sensors per finger × 3 axes (X, Y, Z) = 60 values total.*
* **Architecture:** A deep neural network consisting of **three hidden layers of 128 neurons each with ReLU activations**, optimized using SGD with an MSE loss function.
  
### 3. Live Visualizer (`visualizer_new.py`)
A real-time visualier to evaluate the model on the zeroing interference goal. 
* **Real-time Inference:** The model predicts interference live as the hand is moved.
* **Signal Subtraction:**
  $$\text{Corrected Reading} = \text{Actual Reading} - \text{MLP Prediction}$$
* **Visual Feedback:**
    * **Blue Line:** Raw sensor magnitude.
    * **Green Line:** Model's predicted interference.
    * **Yellow Line:** The compensated signal..
---

## Installation & Usage

### Prerequisites
* Python 3.x
* [PyBullet](https://pybullet.org/)
* PyTorch
* LeapHand SDK

### Getting Started
1. **Clone the repository:**
```bash
 git clone [https://github.com/pianapolataa/anyskin-leaphand.git](https://github.com/pianapolataa/anyskin-leaphand.git)
 cd anyskin-leaphand
 ```
2. **Install dependencies**
3. **Run the visualizer:**
 ```bash
 python visualizer_new.py
 ```
  
Normalized data with difference < 350: [logdata350](https://drive.google.com/file/d/1f2vuUA7VkU_qyLgsN1FqzrpJwi4oncCP/view?usp=drive_link)  
Normalized data with difference < 600: [logdatafixed](https://drive.google.com/file/d/1MvodyUhK8-soA8Yj3Are68ANQtF1y9hN/view?usp=drive_link)  
Full unfiltered data with subtracted baseline: [subdata1](https://drive.google.com/file/d/1rDsLathlZb4y40NXwxksFcAQKbOxpiTm/view?usp=drive_link)  
