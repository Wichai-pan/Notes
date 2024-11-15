# RP6 Localisation

Objectives:

1. Differential drive robots
2. Localisation
3. Motion control



book: Introduction to Mobile Robot Control, Spyros G. Tzafestas, 2014



**Mobile robots**

- A robot is an artificial physical agent that perceives its environment through sensors and acts upon that environment through actuators

<img src="https://wichaiblog-1316355194.cos.ap-hongkong.myqcloud.com/image-20241111091030152.png" alt="image-20241111091030152" style="zoom:67%;" />



**Driving robot around?**

- What does it take to drive a robot from point A to B?

<img src="https://wichaiblog-1316355194.cos.ap-hongkong.myqcloud.com/image-20241111091134850.png" alt="image-20241111091134850" style="zoom:67%;" />

-- self control self decision 





## **Divide and Conquer 分而治之**

- The world is dynamic and fundamentally unknown

- The controller must be able to respond to environmental conditions

- Instead of building one complicated controller – divide and conquer: Behaviors

  ➢ Go-to-goal

  ➢ Avoid-obstacles

  ➢ Follow-path

  ➢ Track-target

  ➢ …







## **Differential drive robots**

- Also known as differential wheeled robots(差速轮式机器人), these are mobile robots whose movement is based on two separately driven wheels placed on either side of the robot body. It can thus change its direction by varying the relative rate of rotation of its wheels, thereby requiring no additional steering motion. 

<img src="https://wichaiblog-1316355194.cos.ap-hongkong.myqcloud.com/image-20241111091931255.png" alt="image-20241111091931255" style="zoom:50%;" />

### **Differential drive **kinematics(运动学)

<img src="https://wichaiblog-1316355194.cos.ap-hongkong.myqcloud.com/image-20241111092013634.png" alt="image-20241111092013634" style="zoom:67%;" />

### **Kinematics of a unicycle**

Ignoring balancing concerns, there are two action variables, i.e., direct inputs to the system in the 𝑋𝑌 plane.

• The first one is the forward/linear velocity: $𝑣=𝜔_𝑢𝑟$, where $𝜔_𝑢$ is the wheel angular velocity, 𝑟 is wheel radius

• The second one is the steering velocity denoted by 𝜔.

![image-20241111092313017](https://wichaiblog-1316355194.cos.ap-hongkong.myqcloud.com/image-20241111092313017.png)

Dynamics:
$$
\begin{aligned}&\dot{x}=v\cdot\mathrm{cos}\theta\\&\dot{y}=v\cdot\mathrm{sin}\theta\\&\dot{\theta}=\omega\end{aligned}
$$
The nonholonomic constraint:
$$
\dot{x}\mathrm{sin}\theta-\dot{y}\mathrm{cos}\theta=0
$$
![image-20241111092848061](https://wichaiblog-1316355194.cos.ap-hongkong.myqcloud.com/image-20241111092848061.png)

- The resultant forward velocity through 𝐶 (the centre of mass) is $v=r(\frac{\omega_r+{\omega_l}}2)$.
- The steering velocity is $\omega=r(\frac{\omega_r-{\omega}_l}l)$.



- Thus, just like the unicycle, the configuration transition equations may be given as

$$
\begin{aligned}&\dot{x}= r \frac{\omega_{r}+\omega_{l}}{2}\mathrm{cos}\theta\\&\dot{y}=r \frac{\omega_{r}+\omega_{l}}{2}\mathrm{sin}\theta\\&\dot{\theta}=r \frac{\omega_{r}-\omega_{l}}{l}\end{aligned}
$$

- Comparing the equations for unicycle and for differential drive yields the transformation
  $$
  \begin{bmatrix}v\\\omega\end{bmatrix}=\begin{bmatrix}r/_2&r/_2\\r/_l&-^r/_l\end{bmatrix}\begin{bmatrix}\omega_r\\\omega_l\end{bmatrix}
  $$
  

## Localisation

- The robot needs to know its location in the environment in order to make proper decisions.
- Localisation can be achieved using:
  - Proprioceptive sensors (encoders, IMU). This type of localisation is named dead reckoning localisation.
  - Exteroceptive sensors (sonar, LiDAR, camera). This type of localisation is named map-based localisation.
  - External sensors (GPS). Not suitable for indoor applications.



### **Probabilistic Localisation**

- In robotics, we deal with localization probabilistically
- Three key components:
  - A robot’s belief of **where** it is (its state)
  - A robot’s **motion** model
  - A robot’s **sensor** model

![image-20241111093920753](https://wichaiblog-1316355194.cos.ap-hongkong.myqcloud.com/image-20241111093920753.png)



### **Motion-based Localisation(Dead Reckoning)**

This technique uses the internal kinematics of the robot to localise it in the environment.

This method is simple to implement, and does not require sophisticated sensors.

However, such technique suffers from the unbounded growth of uncertainty about the robot pose over time due to the numerical integration and accumulation of error.

![image-20241111094300654](https://wichaiblog-1316355194.cos.ap-hongkong.myqcloud.com/image-20241111094300654.png)













