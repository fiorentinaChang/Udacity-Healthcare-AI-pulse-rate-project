Overview
This project has 2 main parts.

Develop a Pulse Rate Algorithm on the given training data. Then Test Your Algorithm and see that it has met the success criteria.
Apply the Pulse Rate Algorithm on a Clinical Application and compute more clinically meaningful features and discover healthcare trends.
Introduction
A core feature that many users expect from their wearable devices is pulse rate estimation. Continuous pulse rate estimation can be informative for many aspects of a wearer's health. Pulse rate during exercise can be a measure of workout intensity and resting heart rate is sometimes used as an overall measure of cardiovascular fitness. In this project you will create a pulse rate estimation algorithm for a wrist-wearable device. Use the information in the Physiological Mechanics of Pulse Rate Estimation section below to inform the design of your algorithm. Make sure that your algorithm conforms to the given Algorithm Specifications in the following concept, Part 1: Overview & Instructions.

Background
Physiological Mechanics of Pulse Rate Estimation
Pulse rate is typically estimated by using the PPG sensor. When the ventricles contract, the capillaries in the wrist fill with blood. The (typically green) light emitted by the PPG sensor is absorbed by red blood cells in these capillaries and the photodetector will see the drop in reflected light. When the blood returns to the heart, fewer red blood cells in the wrist absorb the light and the photodetector sees an increase in reflected light. The period of this oscillating waveform is the pulse rate.


PPG Sensor on Blood Flow

However, the heart beating is not the only phenomenon that modulates the PPG signal. Blood in the wrist is fluid, and arm movement will cause the blood to move correspondingly. During exercise, like walking or running, we see another periodic signal in the PPG due to this arm motion. Our pulse rate estimator has to be careful not to confuse this periodic signal with the pulse rate.

We can use the accelerometer signal of our wearable device to help us keep track of which periodic signal is caused by motion. Because the accelerometer is only sensing arm motion, any periodic signal in the accelerometer is likely not due to the heart beating, and only due to the arm motion. If our pulse rate estimator is picking a frequency that's strong in the accelerometer, it may be making a mistake.

All estimators will have some amount of error. How much error is tolerable depends on the application. If we were using these pulse rate estimates to compute long term trends over months, then we may be more robust to higher error variance. However, if we wanted to give information back to the user about a specific workout or night of sleep, we would require a much lower error.

Algorithm Confidence and Availability
Many machine learning algorithms produce outputs that can be used to estimate their per-result error. For example, in logistic regression, you can use the predicted class probabilities to quantify trust in the classification. A classification where one class has a very high probability is probably more accurate than one where all classes have similar probabilities. Certainly, this method is not perfect and won't perfectly rank-order estimates based on error. But if accurate enough, it allows consumers of the algorithm more flexibility in how to use it. We call this estimation of the algorithm's error the confidence.

In pulse rate estimation, having a confidence value can be useful if a user wants just a handful of high-quality pulse rate estimate per night. They can use the confidence algorithm to select the 20 most confident estimates at night and ignore the rest of the outputs. Confidence estimates can also be used to set the point on the error curve that we want to operate at by sacrificing the number of estimates that are considered valid. There is a trade-off between availability and error. For example, if we want to operate at 10% availability, we look at our training dataset to determine the confidence threshold for which 10% of the estimates pass. Then if only if an estimate's confidence value is above that threshold, do we consider it valid. See the error vs. availability curve below.

Building a confidence algorithm for pulse rate estimation is a little tricker than logistic regression because intuitively, there isn't some transformation of the algorithm output that can make a good confidence score. However, by understanding our algorithm behavior, we can come up with some general ideas that might create a good confidence algorithm. For example, if our algorithm is picking a strong frequency component that's not present in the accelerometer, we can be relatively confident in the estimate. Turn this idea into an algorithm by quantifying "strong frequency component".
