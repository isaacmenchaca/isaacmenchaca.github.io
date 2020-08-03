---
layout: post # if this were a page, you would write "page" instead. They layouts are subtly different. Try it to see what happens.
title:  "Neuromatch Academy Project"
subtitle: "Decoding Color Content from Neural Data and Multi-output Linear Models"
date:   2020-08-02 02:45:13 -0400
author:  # you can get rid of this entire line in your own blog posts, and the page will display the name of the site's owner, taken from the _config.yml file.
background: '/img/posts/06.jpg' # notice how the image referenced is in your project's /img/posts/ folder.
---

Neuromatch Academy (NMA) was an amazing three-week experience that brought upon intellectual knowledge, a sense of community, and most importantly, my re-found curiosity in the analysis of neural data once again.

Along with all the greatness from NMA, I had the opportunity to work with other curious-driven individuals and explore a new perspective on the Kay-Gallant dataset[^fn1].

---
#### Dataset and NMA Project Question

The dataset contains fMRI data for human subjects viewing grayscale naturalistic images. The fMRI data contains BOLD response amplitudes (Z) for voxels representing different functional areas in the visual cortex. Our goal for this dataset  was to determine how color memory representations are shared between the Extrastriate Visual Cortex (V4) and the Primary Visual Cortex (V1). In doing so, we wanted to try to decode inferred color representations from the grayscale images using the appropriate fMRI voxels.

<!-- ![alt text](/img/posts/post1images/NMAimg1.png) -->
<img src="/img/posts/post1images/NMAimage1.png" style="display: block; width:350px; height:200px; margin-right: auto; margin-left: auto;"/>
<div style="text-align:center"><span style="color:black; font-family:Computer Modern; font-size:1; font-style: italic">Figure 1. Example of grayscale naturalistic images from the Kay-Gallant dataset.</span></div>

<img src="/img/posts/post1images/NMAimage2.png" style="display: block; width:350px; height:200px; margin-right: auto; margin-left: auto;"/>
<div style="text-align:center"><span style="color:black; font-family:Computer Modern; font-size:1; font-style: italic;">Figure 2. Response amplitudes of voxels to a corresponding image.</span></div>

---

#### Literature Search

Humans are able to recognize the colors of objects under vastly different illuminations. As part of this process, we use our existing knowledge of an object's color to inform our percept. Previous studies have shown that when people view grayscale images of objects with canonical color content, like yellow bananas and red coke cans, the color of the object can be decoded from both V4 to V1 (Fig. 3A)[^fn2]. However, when people are asked to simply imagine the object, rather than viewing a grayscale image, the color can only be decoded from V4 activation (Fig. 3B)[^fn3]. We wanted to see whether grayscale naturalistic images evoked decodable color representations in V4 and whether those representations were shared with V1.

<img src="/img/posts/post1images/NMAimage3.png" style="display: block; width:350px; height:200px; margin-right: auto; margin-left: auto;"/>
<div style="text-align:center"><span style="color:black; font-family:Computer Modern; font-size:1; font-style: italic;">Figure 3. Participants presented with grayscale images of objects with identifiable color content, color was recoverable from both V1 and V4 (A). When asked to think about the same objects, color recovered only from V4. (B)</span></div>



TO DO: Update!! :)


[^fn1]: Kay, K., Naselaris, T., Prenger, R. et al. Identifying natural images from human brain activity. Nature 452, 352–355 (2008). https://doi.org/10.1038/nature06713
[^fn2]: Bannert, M. M., & Bartels, A. (2013). Decoding the Yellow of a Gray Banana. Current Biology, 23(22), 2268–2272. https://doi.org/10.1016/j.cub.2013.09.016
[^fn3]: Bannert, M. M., & Bartels, A. (2018). Human V4 Activity Patterns Predict Behavioral Performance in Imagery of Object Color. Journal of Neuroscience, 38(15), 3657–3668. https://doi.org/10.1523/JNEUROSCI.2307-17.2018
