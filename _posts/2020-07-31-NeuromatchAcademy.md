---
layout: post # if this were a page, you would write "page" instead. They layouts are subtly different. Try it to see what happens.
# set methods to true so it goes to methods section, otherwise if just to project section set to false
methods: true
title:  "Neuromatch Academy Project"
subtitle: "Decoding Color Content from Neural Data and Multi-output Linear Models"
date:   2020-07-31 12:45:13 -0400
author:  # you can get rid of this entire line in your own blog posts, and the page will display the name of the site's owner, taken from the _config.yml file.
background: '/img/posts/06.jpg' # notice how the image referenced is in your project's /img/posts/ folder.
---

Neuromatch Academy (NMA) brought upon intellectual knowledge, a sense of community, and most importantly, my re-found curiosity in the analysis of neural data once again.

In addition to the training from NMA, I had the opportunity to work with other curious-driven individuals and explore a new perspective on the Kay-Gallant dataset[^fn1].

---
#### Dataset and NMA Project Question

The dataset contains fMRI data for human subjects viewing grayscale naturalistic images. The fMRI data contains BOLD response amplitudes (Z) for voxels representing different functional areas in the visual cortex. Our goal for this dataset  was to determine how color memory representations are shared between the Extrastriate Visual Cortex (V4) and the Primary Visual Cortex (V1). In doing so, we wanted to try to decode inferred color representations from the grayscale images using the appropriate fMRI voxels.

<!-- ![alt text](/img/posts/post1images/NMAimg1.png) -->
<img src="/img/posts/post1images/NMAimage1.png" style="display: block; width:350px; height:200px; margin-right: auto; margin-left: auto;"/>
<div style="text-align:center"><span style="color:black; font-family:Computer Modern; font-size:1; font-style: italic">Figure 1. Example of grayscale naturalistic images from the Kay-Gallant dataset.</span></div>

<img src="/img/posts/post1images/NMAimage2.png" style="display: block; width:350px; height:200px; margin-right: auto; margin-left: auto;"/>
<div style="text-align:center"><span style="color:black; font-family:Computer Modern; font-size:1; font-style: italic;">Figure 2. Response amplitudes of voxels to corresponding images.</span></div>

---

#### Literature Search

Humans are able to recognize the colors of objects under vastly different illuminations. As part of this process, we use our existing knowledge of an object's color to inform our percept. Previous studies have shown that when people view grayscale images of objects with canonical color content, like yellow bananas and red coke cans, the color of the object can be decoded from both V4 to V1 (Fig. 3A)[^fn2]. However, when people are asked to simply imagine the object, rather than viewing a grayscale image, the color can only be decoded from V4 activation (Fig. 3B)[^fn3]. We wanted to see whether grayscale naturalistic images evoked decodable color representations in V4 and whether those representations were shared with V1.

<img src="/img/posts/post1images/NMAimage3.png" style="display: block; width:350px; height:200px; margin-right: auto; margin-left: auto;"/>
<div style="text-align:center"><span style="color:black; font-family:Computer Modern; font-size:1; font-style: italic;">Figure 3. Participants presented with grayscale images of objects with identifiable color content; color was recoverable from both V1 and V4 (A). When asked to think about the same objects, color was recovered only from V4. (B)</span></div>

---

#### Re-colorization and Dominant Color Selection

Because the naturalistic grayscale images from the dataset has no link to color, the first step was to re-colorize the images. This proved a bit tougher than expected as the images feature a very diverse set of subjects containing both simple and complex geometry and lighting alike, and we ended up having to generate three different colorized datasets using two different colorization algorithms in order to retain a sufficiently large number of images. We generated one set using a deep learning colorization API by Hellth Sorgsvartvras, and another with deOldify[^fn4], a self-attention GAN based machine learning tool. In the case of the latter, we generated one set using a render factor (i.e. a rendering resolution) of 8 and another with a render factor of 13.

We then picked the version of each image that had the most immediately identifiable dominant color and discarded any images with no such color. K-Means clustering was then used on the HSV pixel values of the selected re-colorized image. From the largest cluster, we selected the centroid value as the dominant color and converted it into a BGR value (Fig. 4).

<img src="/img/posts/post1images/NMAimage5.png" style="display: block; width:350px; height:100px; margin-right: auto; margin-left: auto;"/>
<img src="/img/posts/post1images/NMAimage4.png" style="display: block; width:350px; height:200px; margin-right: auto; margin-left: auto;"/>
<div style="text-align:center"><span style="color:black; font-family:Computer Modern; font-size:1; font-style: italic;">Figure 4. Three re-colorization techniques adjacent to each other; RF-8 image was selected for the study (A). K-Means clustering of HSV pixel values of an image (B).</span></div>

---

#### Model Selection and Evaluation

Our intent was to decode RGB color values using information from only V1 and V4 voxels, separately. With a time constraint and maintaining simplicity for a decoding model, implementing a linear model for both regions was our go-to choice. With the appropriate voxels as the input and the dominant RGB values as our labels, two separate Multi-output  Regression models needed to be decided on. After splitting the data into a train and test set (75% and 25 % of data, respectively), default parameters for Linear Regression (Ordinary Least Squares), Ridge Regression (L2 regularization), Lasso Regression (L1 regularization), and Elastic Net (Mixed L1 and L2 regularization) on Scikit Learn were evaluated against each other by observing their RMSE performance on the training and testing set (Fig. 5).

From first glance, the OLS and Ridge models were extremely overfitting the data for both V1 and V4. Lasso and Elastic Net performed better on the test set than the previous two models, but this was at the expense of error from the training set despite it seeming to overfit as well. The models were also evaluated using 10-fold cross-validation, which agreed with previous observations.


<img src="/img/posts/post1images/NMAimage6.png" style="display: block; width:350px; height:200px; margin-right: auto; margin-left: auto;"/>
<img src="/img/posts/post1images/NMAimage7.png" style="display: block; width:350px; height:200px; margin-right: auto; margin-left: auto;"/>
<div style="text-align:center"><span style="color:black; font-family:Computer Modern; font-size:1; font-style: italic;">Figure 5. Linear Regression (OLS), Ridge (&alpha; = 0.1), Lasso (&alpha; = 0.1), and Elastic Net (&alpha; = 0.1, l1_ratio = 0.5) were evaluated via RMSE on training and test sets (A), and then evaluated again via a 10-fold Cross-Validation (B).</span></div>

In attempt to improve model performance, the linear models with regularization underwent hyper-parameter tuning using the Scikit Learn GridSearchCV method. While evaluating the best models, it was acknowledged that a tuned Ridge model (L2 &alpha; = 600) performed similar to that of the default Lasso model (L1 &alpha; = 10)(Fig. 6), the better performing model by far along with the default Elastic Net (&alpha; = 0.1, l1_ratio = 0.5).


<img src="/img/posts/post1images/NMAimage8.png" style="display: block; width:300px; height:200px; margin-right: auto; margin-left: auto;"/>
<div style="text-align:center"><span style="color:black; font-family:Computer Modern; font-size:1; font-style: italic;">Figure 6. 10-fold cross-validation comparison between the default Lasso and hyper-parameter tuned Ridge proved to be similar in RMSE performance. </span></div>

---

#### Predictions and Observations

We made predictions using the test set on the Lasso (L1 &alpha; = 10) and Ridge (L2 &alpha; = 600) regression models. The predictions it made were relatively similar for both V1 and V4, and on some images it seemed as if there was promise in what the models were predicting; however, when looking at the entire range of predictions there was bias towards low saturations -- meaning it was unlikely that the models were sampling the entire color space.

To test whether our regressions were catching real differences in color or just guiding the results towards low-saturation values, we shuffled the mappings between our test images and their paired voxel activation patterns, then attempted to predict the dominant color of the randomly-paired image. We repeated this shuffling 1,000 times to create a distribution of the RMSE values. Sure enough, our RMSE from the actual test set fell well within the 95% confidence intervals of this distribution, which indicates that we did not accurately predict images’ dominant colors from their associated voxel activations in either V4 or V1 (Fig. 7A). If you take a look at the colors we predicted for the high-saturation image of raspberries and the low-saturation of the starfish, you can clearly see this bias in action (Fig. 7B). Although the starfish seems to have been predicted close to the dominant color, when looking at the image of the rasberries you can see the model is predicting it to be a low-saturation color; in fact its prediction looks similar to that of the starfish image. Both the default Lasso and the hyper-parameter tuned Ridge models had this bias.

<img src="/img/posts/post1images/NMAimage9.png" style="display: block; width:450px; height:200px; margin-right: auto; margin-left: auto;"/>
<img src="/img/posts/post1images/NMAimage10.png" style="display: block; width:450px; height:250px; margin-right: auto; margin-left: auto;"/>
<div style="text-align:center"><span style="color:black; font-family:Computer Modern; font-size:1; font-style: italic;">Figure 7.  Testing color bias with bootstrap RMSE values of predicted dominant color of randomly-paired images with 95 % conf. interval (A). Demonstrating the bias for low-saturation images demonstrated from bootstrap RMSE values in Lasso models for V1 and V4 (B). </span></div>

It turns out while inspecting the predictions, there indeed was a bias, or more so, the data was so complex that the models' regularizations geared towards the mean of the values. This makes more sense, since having a high L2 &alpha; regularization leaves weights very close to zero, which explains a flat line going through the data's mean in the tuned Ridge models (the representational area of low saturated color). A high L1 &alpha; will also have this effect in the Lasso models, leaving many coefficient weights to zero and increasing sparsity. Overall, a high regularization decreases the variance while it increases the bias, leading to underfitting models that make predictions close to the baseline.


---

#### Future Directions and Final Thoughts

In this dataset, we obtained many images with mixed color content that produced uncertainty in the re-colorization process. For future experimentation, we suggest utilizing more images focused on strong canonical hue (e.g. yellow banana, red coke can) while labeling the true dominant color to avoid uncertainty with re-colorization. We also suggest taking into consideration simple and complex object shapes that are a part of the image. The motivation behind this idea is to not only observe if we can distinguish shapes from the images, but to also limit creating shape bias as well, such as observing horizontal lines due to bodies of water,  or vertical lines due to trees. Most importantly, due to the high measure of features and complexity of predicting color content from voxels, it is highly suggested a more complex model should be explored (such as a deep learning model) to decode the color representations from high dimensional features. In the end we were not able to effectively predict dominant color using our models, but it was interesting to understand why we most likely were not able to.

Although it was not the goal we wanted to achieve, NMA introduced a foundation on how to approach a project: come up with a question, undergo literature review, observe which models to use, evaluate models with appropriate metrics, and repeat. NMA was a really enjoyable three week experience, learning new topics such as deep learning (on the very last days unfortunately) and collaborating on a project with new friends/ peers.

Contributors to this project: Kathryn O'Nell (University of Oxford), Czarinah Micah Rodriguez (UC Berkeley), Kevin Rusch (University of London), and myself, Isaac Menchaca (UC Riverside).

#### Poster and Video for Neuromatch Academy

<img src="/img/posts/post1images/NMAimage11.png" style="display: block; width:600px; height:400px; margin-right: auto; margin-left: auto;"/>

<center><iframe width="600" height="400" src="https://www.youtube.com/embed/mwxZpMvGZuQ" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe></center>

---
[^fn1]: Kay, K., Naselaris, T., Prenger, R. et al. Identifying natural images from human brain activity. Nature 452, 352–355 (2008). https://doi.org/10.1038/nature06713
[^fn2]: Bannert, M. M., & Bartels, A. (2013). Decoding the Yellow of a Gray Banana. Current Biology, 23(22), 2268–2272. https://doi.org/10.1016/j.cub.2013.09.016
[^fn3]: Bannert, M. M., & Bartels, A. (2018). Human V4 Activity Patterns Predict Behavioral Performance in Imagery of Object Color. Journal of Neuroscience, 38(15), 3657–3668. https://doi.org/10.1523/JNEUROSCI.2307-17.2018
[^fn4]: Jason Antic. jantic/deoldify: A deep learning based project for colorizing and restoring old images (and video!). https://github.com/jantic/DeOldify, 2019.
