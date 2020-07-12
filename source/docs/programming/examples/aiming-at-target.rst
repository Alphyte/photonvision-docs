Aiming at a Target
==================

You can use yaw angles from PhotonLib as error values in a P and arbitrary feedforward controller so you can automatically align yourself to a target, reducing cycle times. The controller can be written as follows:

.. math:: u(t) = K_p \cdot e(t) + K_{arbFF} \cdot sgn(e(t))

where :math:`u(t)` is the input given to the drivetrain. :math:`K_p` defines a software "spring" that exerts a "force", or gives an input proportional to the error. :math:`K_{arbFF}` adds the "force", or a value to the input that is needed to overcome friction, so that the drivetrain will rotate for small errors.

Implementation
--------------

The code to implement the controller described above is below.

.. tabs::

   .. code-tab:: java
   
      double yaw = target.getYaw(); // Yaw error
      double kP = 0.08; // Proportional coefficient
      double kArbFF = 0.05; // Arbitrary feedforward

      if (Math.abs(kArbFF) > 1.0) { // You can modify this value based on how much precision you need
         double input = yaw * kP + Math.copySign(kArbFF, yaw);
         drive.tankDrive(input, -input); // Rotates the drivetrain
      }

   .. code-tab:: c++

      // std::abs and std::copysign are available in the cmath header
      double yaw = target.GetYaw(); // Yaw error
      double kP = 0.08; // Proportional coefficient
      double kArbFF = 0.05; // Arbitrary feedforward

      if (std::abs(yaw) > 1.0) { // You can modify this value based on how much precision you need
         double input = yaw * kP + std::copysign(kArbFF, yaw);
         drive.TankDrive(input, -input); // Rotates the drivetrain
      }
      

.. note::
   The gains in this example were arbitrarily chosen. You may need to tune your gains on a per-robot or per-game basis. The next section will go over how to tune the gains.

Tuning
------

To tune your controller, first tune your :math:`K_{arbFF}` value. Set :math:`K_p` to 0, and set :math:`K_{arbFF}` initially to 0.01. Place the robot where the camera can see the target, but the robot is not looking directly at the target. Then, increase the :math:`K_{arbFF}` by small increments (~0.005) until the robot just starts to rotate.

Then, in order to tune your :math:`K_p` value, start a low value, like 0.025. Like :math:`K_{arbFF}`, place the robot where the camera can see the target, but the robot isn't looking directly at the target. Increase :math:`K_p` by small increments (~0.005) until the robot starts to oscillate, then go back to the largest :math:`K_p` where the robot didn't oscillate.

