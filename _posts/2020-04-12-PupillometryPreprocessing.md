---
layout: post # if this were a page, you would write "page" instead. They layouts are subtly different. Try it to see what happens.
# set methods to true so it goes to methods section, otherwise if just to project section set to false
methods: true
title:  "Pupillometry Preprocessing"
subtitle: "Cleaning data to use pupil size as an accurate measure of LC BOLD activity"
date:   2020-04-12 12:45:13 -0400
author:  # you can get rid of this entire line in your own blog posts, and the page will display the name of the site's owner, taken from the _config.yml file.
background: '/img/posts/04.jpg' # notice how the image referenced is in your project's /img/posts/ folder.
---

Pupillometry can be used as an indirect marker of cognitive behavior and has been used on human subjects due to its safe, convenient, and  powerful advantages in studying the human brain. Moreover, when explaining the influential neuroanatomy of a task-evoked pupillary response, evidence points to the Locus Coeruleus (LC). (put citation.) The LC circuit is the main source of norepinephrine in the brain which is involved in cognitive functions related to arousal including attention, stress, and the sleep-wake cycle [^fn1] [^fn2] [^fn3].

In our study, pupillometry data was collected as a noninvasive proxy of LC activity. Using pupil size as a surrogate for LC activity is a method supported in literature reporting strong correlations between pupillometry data and LC [^fn4]. Unfortunately, in order to verify pupil size as an accurate measure of LC BOLD activity, pupillometry data must be cleaned from blinks and signal drop out. These issues were regressed out using the ET-remove-artifacts Matlab toolbox created from a collaborating lab at USC [^fn5]. This serves as a guide on how I used this toolbox to clean the pupillometry data.

---
#### Structuring the Data

The application accepts pupillometry data formatted in a struct data type. This variable needs to be named **S** in order for the GUI to accept it as an input. The data structure may contain pupil data from multiple sessions, where each row contains data from a different session. Therefore, we used the indices of **S** to represent the human subjects. These indices contain a struct field named **data** with the required and optional sub-fields: **sample**, **smp_timestamp**, and **valid (optional)**.

*   **sample**: was assigned the raw pupil values stored as a Nx1 numerical array of the session.
*   **smp_timestamp**: was assigned the sample timestamps stored as a Nx1 numerical array (units: seconds).
*   **valid (optional)**: was used to represent the valid/invalid sample tag provided by the eye-tracker. Values were stored as a Nx1 logical array (1 = valid, 0 = invalid); this can be used to enhance interpolation of artifacts within the toolbox.

Note: the array sizes for the variables in **data** must be of the same size (Fig. 1).

<img src="/img/posts/post2images/pupilimage1.png" style="display: block; width:350px; height:200px; margin-right: auto; margin-left: auto;"/>
<div style="text-align:center"><span style="color:black; font-family:Computer Modern; font-size:1; font-style: italic;"> Figure 1. Variable S as a struct type containing indices/ rows that represent each human participant (left). The data struct in each index in S contains the sample, smp_timestamp, and valid arrays for a subject's run (right). </span></div>

---
#### Loading Data and Automating Cleaning

The toolbox can be downloaded from the github repository [^fn5]. Once downloaded, I recommend setting the folders into your path. Once that is done, you can call the toolbox GUI in the current directory with **ET_RemoveArtifacts_App** on the Command Window. You can then load your data via **file** > **load data structure** on the top left of the interface. Once the data is loaded, the toolbox should automatically run its artifact removal algorithm on the first run using its default parameters. The algorithm detects artifacts and performs a linear interpolation where it is present. For the desired parameters, we set the **Resampling Rate (Hz)** to the sampling rate of the eye-tracker (2000 Hz) and selected the **Detect Invalid Samples** option with a **Front and Rear Padding (s)** of 0.03. The rest of parameters were left in default, and the algorithm on the new parameters were ran using the **Apply Algorithm** button (Fig. 2).

<img src="/img/posts/post2images/pupilGIF1.gif" style="display: block; width:650px; height:500px; margin-right: auto; margin-left: auto;"/>
<div style="text-align:center"><span style="color:black; font-family:Computer Modern; font-size:1; font-style: italic;"> Figure 2. Inputting data that has been unseen by the toolbox will undergo a default automatic run with default parameters. You can adjust the parameters and run the algorithm again with the <b>Apply Algorithm</b> button. Once desired parameters are set, the parameters will be the same for the rest of the runs unless changed. </span></div>

The result of the cleaned data can be seen in  **Pupil Plot** section of the GUI where the green represents the original data with its artifacts and the red indicates the reconstruction of the original data via the toolbox algorithm.

---
#### Manual Inspection of Data Quality

In most cases the toolbox will clean most of the data, but there may be instances that are missed. Because the pupillometry data is sampled at 2,000 Hz, it is sampled at a much higher frequency than which the algorithm is accustomed and may potentially cause some blinks to go undetected. Furthermore, the data is often very noisy and sudden spikes unrelated to blinks may arise from inadequate calibration of the eye-tracking system during data collection. These factors can cause the algorithm to be incapable of detecting and interpolating all signal drop outs present. In these cases, regions where the noise outweighs the signal were manually interpolated to enhance the signal-to-noise ratio.  Only in extreme cases where there is a significant amount of noise that cannot be recovered (> 2 seconds) is the data conservatively replaced with NaNs. Doing this required a manual inspection to be done on all runs for a data quality check (Fig. 3). To do this, you simply select the **Manually Edit Plot** button for that run on the GUI.

<img src="/img/posts/post2images/pupilGIF2.gif" style="display: block; width:650px; height:500px; margin-right: auto; margin-left: auto;"/>
<div style="text-align:center"><span style="color:black; font-family:Computer Modern; font-size:1; font-style: italic;"> Figure 3. Manually inspecting and cleaning data. After the run is fully cleaned, select <b>Save Changes and Exit</b>. </span></div>


There are three functions to apply during this process:
*   **Interpolate**: linearly interpolates over the select region.
*   **Re-populate**: replaces the selected region with original (non-reconstructed) data.
*   **NaN Data**: replaces the selected region with NaNs.

Some hot keys to use during the manual inspection:
*   **WASD**: arrow keys to navigate plot (AD: horizontal keys; SW: vertical keys)
*   **Q**: select start point
*   **E**: select end point
*   **Z**: Interpolate
*   **X**: Re-populate
*   **C**: NaN Data

Once done, you can repeat the entire cleaning process on all runs.

---
#### Potential Issues and Troubleshooting

It is recommended that you constantly save your work in a new .mat file after every other run. You can do this by selecting **file** > **Save As**. This can solve many issues and makes sure your efforts were not in vain. Here are some issues I have encountered before:

*   **Not enough memory in system**: There are instances where a file may not save correctly due low memory on a computer system. If an individual does not notice the error on the Command Window and closes the toolbox GUI, the work will not be saved and you will not be able to retrieve the changes since your last saved .mat file. To validate if it saved correctly, check if you are able to load a **S** struct out of the .mat file. Otherwise, it did not save.

*   **S variable in .m file is too large**: When using the toolbox to clean data, additional struct variables are stored in the **S** struct when saved (Fig 4). This will make your **S** struct larger than before, and a single variable cannot exceed 2GB in Matlab. If this limit is exceeded, you cannot appropriately save your work and there is no way to revert the changes while in the toolbox. This also means your work will be lost since your last saving point. If this happens and you have a previous saving point in a .mat file, a hacky solution in the toolbox will be to go back to previous saving point runs and drastically lower the resampling frequency. Doing this will reduce the storage and allow you to save the new cleaned runs. You can then simply retrace the previous, non-manipulated runs from previous .mat saving points. An additional solution that is highly recommended is to clean the input data by sets of 5 or 10 and then save them in separate .m files. This can be done while maintaining the index-identities of the data. Simply insert the initial, raw data into the toolbox and clean runs 1-5, then insert the raw data again and clean runs 6-10, and so on. Doing it this way will avoid the variable storage issue as the ranged sets have their own .mat file.


<img src="/img/posts/post2images/pupilimage2.png" style="display: block; width:400px; height:200px; margin-right: auto; margin-left: auto;"/>
<div style="text-align:center"><span style="color:black; font-family:Computer Modern; font-size:1; font-style: italic;"> Figure 4. Once runs are cleaned, additional data is stored for those runs thus increasing the storage in <b>S</b> when saved. In this example, only runs 1-4 have been cleaned by the toolbox.</span></div>

*   **Inconsistent Timing**: Another error that has been previously noticed is when the eye-tracker system spits random numbers before the actual start time for a run. If you get an error when switching to a subject and the GUI becomes unresponsive, look at your time data for that subject as it must be consistent. One way to check is by using the following command on Matlab (Fig. 5): plot(S(index_of_subject).data.smp_timestamp).

<img src="/img/posts/post2images/pupilimage3.png" style="display: block; width:400px; height:200px; margin-right: auto; margin-left: auto;"/>
<div style="text-align:center"><span style="color:black; font-family:Computer Modern; font-size:1; font-style: italic;"> Figure 5. Example of consistent timing of eye-tracker. </span></div>




Once, again **saving periodically to a new .m file** will help with most issues. Do this to prevent your hard work from vanishing!


---

Note: In case of version capability for future reference, I cloned the version that was used and pushed the code here (put pushed version here).

Hope this was useful!

---
[^fn1]: Song, Andrew H., et al. "Pharmacological modulation of noradrenergic arousal circuitry disrupts functional connectivity of the locus ceruleus in humans." Journal of Neuroscience 37.29 (2017): 6938-6945.
[^fn2]: Sara, Susan J. "The locus coeruleus and noradrenergic modulation of cognition." Nature reviews neuroscience 10.3 (2009): 211-223.
[^fn3]: Guedj, Carole, et al. "Could LC-NE-dependent adjustment of neural gain drive functional brain network reorganization?." Neural plasticity 2017 (2017).
[^fn4]: Murphy, Peter R., et al. "Pupil diameter covaries with BOLD activity in human locus coeruleus." Human brain mapping 35.8 (2014): 4140-4154.
[^fn5]: Huang R. ET-remove-artifacts. GitHub; 2020. https://github.com/EmotionCognitionLab/ET-remove-artifacts
