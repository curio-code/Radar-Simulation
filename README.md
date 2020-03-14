# Radar-Simulation

![Alt Text](https://github.com/curio-code/Radar-Simulation/blob/master/media/output.gif)

## Simulation Environment
1.  Defining an empty scenario
```
scenario = drivingScenario;
scenario.SampleTime = 0.01;
```
2. Adding roads in into the environment
```
roadCenters = [0 0; 50 0; 100 0; 250 20; 500 40];
road(scenario, roadCenters, 'lanes',lanespec(2));
```

3. Creating Ego Vehicle and various traffic cars on the road
```
egoCar = vehicle(scenario, 'ClassID', 1);
trajectory(egoCar, roadCenters(2:end,:) - [0 1.8], 25); % On right lane

% Add a car in front of the ego vehicle
leadCar = vehicle(scenario, 'ClassID', 1);
trajectory(leadCar, [70 0; roadCenters(3:end,:)] - [0 1.8], 25); % On right lane

% Add a car that travels at 35 m/s along the road and passes the ego vehicle
passingCar = vehicle(scenario, 'ClassID', 1);
waypoints = [0 -1.8; 50 1.8; 100 1.8; 250 21.8; 400 32.2; 500 38.2];
trajectory(passingCar, waypoints, 35);

% Add a car behind the ego vehicle
chaseCar = vehicle(scenario, 'ClassID', 1);
trajectory(chaseCar, [25 0; roadCenters(2:end,:)] - [0 1.8], 25); % On right lane
```
4. Attaching 6 RADARs on the Ego Vehicle
```
sensors{1} = radarDetectionGenerator('SensorIndex', 1, 'Height', 0.2, 'MaxRange', 174, ... 
    'SensorLocation', [egoCar.Wheelbase + egoCar.FrontOverhang, 0], 'FieldOfView', [20, 5]);
```

## Create a Kalman filter for tracking
```
H = [1 0 0 0; 0 0 1 0; 0 1 0 0; 0 0 0 1];
filter = trackingKF('MotionModel', '2D Constant Velocity', 'State', H'*detection.Measurement, 'MeasurementModel', H, 'StateCovariance', H'*detection.MeasurementNoise*H, 'MeasurementNoise', detection.MeasurementNoise);
```
### Full flow of the code
![Alt Text](https://github.com/curio-code/Radar-Simulation/blob/master/media/image6.png)
