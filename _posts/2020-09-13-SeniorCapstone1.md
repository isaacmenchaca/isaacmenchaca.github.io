---
layout: post # if this were a page, you would write "page" instead. They layouts are subtly different. Try it to see what happens.
# set methods to true so it goes to methods section, otherwise if just to project section set to false
methods: true
title:  "Senior Capstone Project: Wake Right App (2019)"
subtitle: "Neural Alarm Clock - Implementation Analysis and Future Direction"
date:   2020-09-13 12:45:13 -0400
author:  # you can get rid of this entire line in your own blog posts, and the page will display the name of the site's owner, taken from the _config.yml file.
background: '/img/posts/06.jpg' # notice how the image referenced is in your project's /img/posts/ folder.
---

#### Introduction: Clinical Problem and Need

Sleep is a natural phenomenon that is necessary for the human body; it affects the brain, heart, mood, immune function, and more [^fn1]. Thus, analysis of sleep cycles provides significant benefits in understanding patients with disrupted sleep patterns, including apnea, narcolepsy, unexplained chronic insomnia, and more. However, the difficulty and cost of effective sleep monitoring is not suited for casual use [^fn2]. Nonetheless, sleep follows the body’s internal clock in which the human brain exhibits chronobiological patterns through emitted electrical activity [^fn3]. This electrical activity, with respect to present technological advancements, can be used to analyze sleep cycles and improve sleep quality.

Wake Right is a mobile application that demonstrates a method to easily analyze sleep and support an individual’s circadian rhythm to avoid the detrimental effects of sleep inertia. Sleep inertia, also known as morning grogginess, is a period of declined motor dexterity and cognitive abilities due to an individual awakening during slow wave sleep (SWS) [^fn4]. Sleep inertia often affects people that use alarm clocks that inevitably disrupts an individual’s current sleep stage in order to wake. Individuals experiencing this feel groggy and also show impaired cognitive abilities and motor dexterity. This directly translates to a drop in productivity at work or in the classroom until the body can adjust itself to waking [^fn1]. This adjustment process can take up to three hours in some cases [^fn6]. The inability to immediately adjust to waking can be detrimental to a professional’s career, especially those in a high-stakes line of work such as, police, EMTs, emergency room doctors, etc. In healthcare specifically, a 36% increase in errors has been reported for those who are working on call or on a lengthy shift [^fn7]. The apparent disorientation can lead to judgement problems by first responders as well as white and blue collar employees. To prevent this, the app uses EEG technology and real time signal processing analysis to promote waking during a more favorable sleep stage.

Currently, there are other sleep apps on the market that aim to identify sleeping patterns and analyze sleep stages. Smart watches use accelerometers and measure heart rate to track the user’s movements when sleeping and uses the data to correlate sleep stages [^fn8]. However, none of these products offer an individualized sleep cycle analysis: movement sensors can be thrown off by sleeping with a partner or having a pet enter the bed; sound sensors can be thrown off by harsh weather, a passing car or barking dog. Wake Right seeks to deliver an individualized smart alarm system based on real time measurements of the brain’s activity.

The Wake Right app was tested in real time for a sleep session, and the data was visually represented to demonstrate the functionality of the Wake Right signal processing and analysis methods along with the alarm’s ability to avoid waking an individual during SWS. Using signal processing techniques, statistical analysis, and data driven science, Wake Right plans to help the way people start their morning by waking up in a light sleep cycle and avoiding sleep inertia.

---
#### Data Acquisition

The Wake Right system is composed of two components: a wearable electroencephalogram (EEG) and a smartphone with iOS  capabilities to install the Wake Right application. The EEG designed by Muse (InteraXon) detects electrical activity in microvolts from the brain in real time. It contains four electrodes located around the left ear (TP9 in the international 10-20 EEG system), left forehead (AF7), right forehead (AF8), and the right ear (TP10) that do the recording, as well as containing 3 references (ground) electrodes on the center of the forehead (Fig. 1).

<img src="/img/posts/post5images/WR1image1.png" style="display: block; width:500px; height:200px; margin-right: auto; margin-left: auto;"/>
<div style="text-align:center"><span style="color:black; font-family:Computer Modern; font-size:1; font-style: italic">Figure 1. 2016 Muse Model MU-02 device and location of the electrodes highlighted in blue.</span></div>

The design of this project relies on the use of an EEG to obtain measurements of the brain’s electrical activity in microvolts, μV. The application will receive the microvolt data in real time and processes it using advanced signal processing methods. In order to accomplish that, analysis is first needed to understand how the signal processing will be implemented. For this reason raw microvolt data for sleep was collected and analyzed before the app implementation. The full code from preprocessing and analysis can be found in this [ipython notebook](https://github.com/isaacmenchaca/isaacmenchaca-Computational-Cognitive-Neuroscience/blob/master/eegSleep/WakeRight%20Analysis.ipynb).

---
#### Data Preprocessing

Preprocessing on the pre-collected data was intended to be as close to the real application scenario as possible; all steps are documented on the link mentioned above. The sleep data was pre-recorded via Muse Labs, which led to instances of data being set to NaNs due to artifact detection flags such as blinking or jaw clenching. This is an aspect that was not intended for analysis because the actual voltage for the app implementation was needed. These NaN values can be seen as red dotted lines in the code below. Interpolation was used in attempt to recover the missing data.

<script src="https://gist.github.com/isaacmenchaca/5ea10e61824c5fc944f6af22124f25cb.js"></script>
<img src="/img/posts/post5images/WR1image2.png" style="display: block; width:550px; height:200px; margin-right: auto; margin-left: auto;"/>

The data points were also received per second, so upsampling to 256 Hz was needed to keep the data as close to the SDK bluetooth streaming as possible. This also helped in building the application algorithm with the bluetooth sampling frequency taken into account. Also, it is also important to note that voltage value outliers were kept in the analysis to keep it as realistic to the application scenario as possible. It was not as simple to clean out outliers in real time as expected; however, this turned out to be a minimal issue in the real implementation. NaN interpolation and resampling was done as below.

<script src="https://gist.github.com/isaacmenchaca/a2a9bfa79083fcfa1d17bb4a9e20b826.js"></script>
<img src="/img/posts/post5images/WR1image3.png" style="display: block; width:550px; height:200px; margin-right: auto; margin-left: auto;"/>

---
#### Time-Frequency Analysis

Time-Frequency Decomposition was performed on the data via short-time FFT (STFT). STFT is a simple extension of the Fourier Transform (FFT). It is a method for extracting time-frequency power and phase information from a signal, and it can be used on EEG data to investigate the time-varying changes in the frequency structure of task data. For more information on how it works, click [here](https://isaacmenchaca.github.io/2020/02/07/EegTF.html) to read a bit more about it. The power was log-transformed, and the window size was 265 (fs, Hz) * 5 data points to represent a window of 5 seconds. The sliding time step is approximately half of the window size (left as a default parameter with scipy package). The code below demonstrates how it was performed.

<script src="https://gist.github.com/isaacmenchaca/ce6c0d5467c37eaf0352888eb2d31bb4.js"></script>
<img src="/img/posts/post5images/WR1image4.png" style="display: block; width:550px; height:350px; margin-right: auto; margin-left: auto;"/>

With a single channel I was able to recognize an increase and decrease in power in the short sleep session. Since we know SWS is represented by an increased presence of lower frequency waves (delta), a delta power band was extracted from the STFT to visualize sleep depth. This was done as below.

<script src="https://gist.github.com/isaacmenchaca/db28ee4c781aa62d1449276e8e4a8ae5.js"></script>
<img src="/img/posts/post5images/WR1image5.png" style="display: block; width:550px; height:350px; margin-right: auto; margin-left: auto;"/>

We are able to see the changes of magnitude of delta power over time. The log-transform of it was also visualized.

<script src="https://gist.github.com/isaacmenchaca/b556ad7437bc305693cf4190d6259e80.js"></script>
<img src="/img/posts/post5images/WR1image6.png" style="display: block; width:550px; height:350px; margin-right: auto; margin-left: auto;"/>

The log-transform of the delta power magnitude enhanced our view of a sleep depth cycle. Having this information we can now use it to determine the design implementation of when to wake an individual on the application.


---
####  Real-Time Functionality

On the application, the interface will allow the user to enter a time window of when they will like to be awakened (demonstrated in the video below). A time window of at least 30 minutes is required to determine when to wake the individual. If the system was not able to make a decision, which is determined by a threshold, it will wake the individual at the last second of the time window.

<img src="/img/posts/post5images/app.mov" style="display: block; width:200px; height:400px; margin-right: auto; margin-left: auto;"/>
<div style="text-align:center"><span style="color:black; font-family:Computer Modern; font-size:1; font-style: italic;"> User Interface. Upon turning on the EEG device, the user enters a desired time window to wake up. The mobile EEG will then connect to the application via bluetooth, and the UI slider is used to indicate the preference of the system's wake-decision threshold strength.</span></div>

Taking the rolling mean in the pre-analysis was only for the purpose of enhancing the view of the sleep depth changing over time. The power in the real-time data collection did not start until there was enough points to undergo the first FFT. There was no zero padding or boundary extension in the application. We also took the absolute value of the log-transform so it is on a positive scale. In real-time, a threshold is used to distinguish the sleep depth. Above the threshold will be when to wake an individual and below the threshold will be when to not wake an individual. Since there is no rolling mean in the app, the wake decision is by majority power vote for every 5 minutes. If more power points are below the threshold for the majority of 5 minutes, the person will not be disturbed. Otherwise, the alarm will ring. The threshold shall not be relevant for the wake decision until the desired time window is reached.

The threshold will be an average from the top 50 and lowest 50 values to bypass real-time artifact influence. These values are collected from the data prior to the sleep window. As seen on the video, there is a UI slider on the app to indicate how conservative or liberal the user wants the system to be in making a wake decision during the alarm window. Leaning towards conservative means making the threshold higher; this decision will ensure a user is awakened during light sleep, but it also carries the risk of making the decision at the last second of the time window if the threshold was not met. Leaning towards a liberal system makes the threshold lower, and a highly liberal system will risk possibly being awakened during SWS (Deep Sleep) during the time window.

An example of the described system is as below, in rolling mean analysis form and in real-time form, respectively.

<script src="https://gist.github.com/isaacmenchaca/15dc374f467017aeb52a9cdecb20ad80.js"></script>
<img src="/img/posts/post5images/WR1image7.png" style="display: block; width:550px; height:350px; margin-right: auto; margin-left: auto;"/>

---
#### Future Directions Ideas:

Along with the threshold, there should be an attempt to predict future values to find the most optimal time to wake up during the sleep window. This will also allow the system to make the decision to wake someone earlier rather than later if necessary. A suggested way to do this is with a Deep Learning model with a Recurrent Neural Network (RNN) architecture. A time series is input into the model, and in return a series of predicted future values will be the output. The downside is it may require many sleep trials for a single user to train the model, as sleep cycles are different per person, but if done correctly it can be effective in avoiding sleep inerta/ morning grogginess.

This idea was experimented with generated time-series data. This function, as well as others, can be found in the same ipython notebook. The following demonstrates the architecture built with corresponding input and connections.

<script src="https://gist.github.com/isaacmenchaca/be80341802bbfcb21eb3c2de9bfcf2e0.js"></script>
<img src="/img/posts/post5images/WR1image8.png" style="display: block; width:300px; height:200px; margin-right: auto; margin-left: auto;"/>
<img src="/img/posts/post5images/WR1image9.png" style="display: block; width:200px; height:250px; margin-right: auto; margin-left: auto;"/>

After building the model architecture, it was trained using Adam optimization. The MSE (Mean-Squared Error) loss was used to demonstrate if the model was learning and not overfitting the training data.

<script src="https://gist.github.com/isaacmenchaca/827cbe515f17ae61ce55ae683d6aa17d.js"></script>
<img src="/img/posts/post5images/WR1image10.png" style="display: block; width:550px; height:200px; margin-right: auto; margin-left: auto;"/>

The model was then evaluated with a generated test dataset. Instances of 50 data points were fed into the model to predict their next 10 data points. The following demonstrates the predictions in comparison to the actual data points.

<script src="https://gist.github.com/isaacmenchaca/3954dae5e16b928b4681b280035ec6c7.js"></script>
<img src="/img/posts/post5images/WR1image11.png" style="display: block; width:550px; height:600px; margin-right: auto; margin-left: auto;"/>


The model demonstrates an ability to predict future values. With the appropriate sleep data collection per user, there is a chance a RNN may help in determining if a user will go into light or deep sleep. Doing so will enable the ability to wake an individual at the most optimal time during the time window.


---

If you have questions to do something similar, feel free to message me.

---
[^fn1]: Mellow ML, Dumuid D, Thacker JS, Dorrian J, Smith AE. Building your best day for healthy brain aging-The neuroprotective effects of optimal time use. Maturitas. 2019;125: 33–40.
[^fn2]: Roebuck A, Monasterio V, Gederi E, Osipov M, Behar J, Malhotra A, et al. A review of signals used in sleep analysis. Physiological Measurement. 2014. pp. R1–R57. doi:10.1088/0967-3334/35/1/r1
[^fn3]: Muñoz-Torres Z, Velasco F, Velasco AL, Del Río-Portilla Y, Corsi-Cabrera M. Electrical activity of the human amygdala during all-night sleep and wakefulness. Clin Neurophysiol. 2018;129: 2118–2126.
[^fn4]: Bruck D, Pisani DL. The effects of sleep inertia on decision-making performance. J Sleep Res. 1999;8: 95–103.
[^fn5]: Acharya U R, Faust O, Kannathal N, Chua T, Laxminarayan S. Non-linear analysis of EEG signals at various sleep stages. Comput Methods Programs Biomed. 2005;80: 37–45.
[^fn6]: Hilditch CJ, Dorrian J, Banks S. Time to wake up: reactive countermeasures to sleep inertia. Ind Health. 2016;54: 528–541.
[^fn7]: Caruso CC. Negative impacts of shiftwork and long work hours. Rehabil Nurs. 2014;39: 16–25.
[^fn8]: Sleep Cycle App: Precise or Placebo? In: Psychology Today. [cited 29 May 2019]. Available: http://www.psychologytoday.com/blog/brain-babble/201310/sleep-cycle-app-precise-or-placebo.
