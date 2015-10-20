---
layout: post
title: "How to install OpenCV 3 on Mac"
author: charlie_gerard
excerpt: "I'm planning on starting a project involving computer vision using the OpenCV framework.
As I've had some troubles getting everything installed and running a basic project, I'm writing this tutorial to help anyone who's going through the same process, hoping it will make it easier ..."
tags: [technology, computer vision, openCV]
comments: true
---

I'm planning on starting a project involving computer vision using the OpenCV framework.
As I've had some troubles getting everything installed and running a basic project, I'm writing this tutorial to help anyone who's going through the same process, hoping it will make it easier :)

##Installing OpenCV

To get started, visit the [OpenCV website](http://opencv.org/) and download the correct version for your OS.
Then, go to the [MacPorts project](https://www.macports.org/install.php), download it and install it.

Once this is done, open a new terminal window, write '<strong>port</strong>' and press enter. If you see the following, you're good.

<img src="https://lh3.googleusercontent.com/i9tMY8UlzN0esEE7P9St7xrTJlMFh2K3ZGvqofV0VpePJeAhNiOuSoKsxRyUOkKUD7BoHTXDXpK48JC51TlruDuW35qc64WNozTXscGKt9L7Q7g5_HWEzigu-97O-aOxAE7h0h1wicOmNrDyNwLn61PypZJA2EySSDrtGKpOXYu1thS_mNt01QEcEmYNnuqsAY5D5hkhv8SQI2rT5kdEqEaEuTzS2TIVZ53nvz4H1yc6kbqjGh12z1c9a7DHzK9qU4yi4SgEfjOR9ioC-ALWKwuCK00trPER9u8-8jRY0u9hBkW3oH3pIXECa0P9E-UCYmnyTlyyUcCh_UAE4vhZCE1KQ_pBS7xvSzLibUa0It-hrXE66WpXB0dwb7MStkk-51J5iRM6xSp8-2q9I4Zga09pzF5Y9P0H1ne9lx7R3pgZetUC3xVT6hdGWcXFPOmYRUz17CMbjc3F-QtZvPPLD20Lzg0sk4DByVxX_7a4okXLUn1gNqXvmwo7PidSqiNCwVDNyAUOgU07BoerX4ctrmEVomwj_XlzSzbi=w1144-h264-no" width="640" height="480">

Find where you downloaded OpenCV and drag it to where you want it to live. Make sure you're not going to change the location often because it could break projects.

Once you moved OpenCV to a directory you're happy with, <strong>cd into this directory</strong> and <strong>create a folder called 'build'</strong> with the mkdir command.

<strong>Cd into this new folder</strong> and run '<strong>cmake -G "Unix Makefiles" ..</strong>'

This command will start installing what OpenCV needs to run.

Once it's done, run "<strong>make -j8</strong>" in your Terminal and '<strong>sudo make install</strong>'.

Everything should now be installed. If you check inside your build > lib folder, you should see all the frameworks that have been installed.

##Now, let's setup a basic project in Xcode.

Open Xcode and create a new project by clicking on File > New > Project.

<img src="https://lh3.googleusercontent.com/x28BVcVztmhAdrjmkmAbrHIA-gJ7OB7jvz8Wkzx0PqOdUGQn-5LxkiEqifznoe8iL7PaiOvldYVlO3SAluPVLn-emP0id2D_Mm57gLMf8CJh-oiTREdKgT2n4FRQfk25ClK-ovC7O_9mt5v2gtfDb-LLh7Q9wDxQXNpjGS_7ShM7TxKXiLGOWZCNgWT_WJ1xXMU_2gygbg2cBPtJzqEUIZj7FUrlpW8a5CYRBZuqqe4Fskl17c2Ul5iVaQSdJPPZ9BildpZtPxSITYSVbkaW_ud3c6kQ08aCmxov2Ka6Dyf1BvtBkPCqizspQ1aTWeWk0KmRaECqs81GN-ZAW7OEiUMwNGDw09fYBWlzLSuDZOZ761veOi2oYJOImLoIy_s9YVHddyk39a08ZmaQsyDxC_aTWL6EkS9kEK9gmoY8y-bFXyKP6F637lhIgzCZCfMWdYX4yt3M56kIaI2FkkopbE5xzfiE9qbvlHDCpohUSJJbLF9hSZvosIxFePP7ePP4jv88rXPE2PlByNgcfBZYwmGlfAMdRb9ZGhkN=w1541-h1002-no">

Go to OSX > Application and select 'Command Line Tool'.

<img src="https://lh3.googleusercontent.com/zw-Xaj0AvxcNxe-ErR8mWCcLQY_Zqs2nucwcscKJhC7bTkifPg-z-1zda-G6Rs7SBfSn0Jjb6vyyUzAMWM2-vOYLRo401E37nvYp9oplwbc7EASF1x624jHoZet4qRSCCAlTJWIFyBPI39dCjz76l3LUR8A1VZ5JWO_NKj-rFwj-1lJkR5TsaHX1prjxpBQ1UckAMBbDj6BzHPETBS4-_CThHGXDfRJ3jGqH13UDP1VLAuVPxl1NEMhOzUWoeezmF7q-6lksSYLKQnk-uWgY2xkpw4b7Yc2twV62izoco3cNY46Qm0NP6WoJobKP4pc2V9v5vBkLma4uxUzo2PnlAJ7lmA7M-6JtUYWpCGPJybWK9scd41U2hJW1GgUrLwvdu7mGwQl_Wcy8MjsOZr8VivLjo24V_wOckR6DyJi6vx3vnXA2kkrDZqgEYEVzdkyphZw-uw_xVNzLaqzWyj3CR-RTIlhhZGb0VrQkTwqMF_FgcysHjC2Lx9WA_VkNk4x1-MOXBG6N2o21Dhky5En5f_tak8eqPgQYnnbF=w1464-h1042-no">

In the next window, make sure the language selected is C++ and enter the project name you'd like. For example 'OpenCV Tutorial'.

Your project window will then open, select the project file (the file at the top of your project), and follow these steps:

1. At the top, select <strong>Build Settings</strong> and search for <strong>Search Paths</strong>
2. Change the <strong>Always Search User Paths</strong> to true.
3. Change the <strong>Header Search Paths</strong> to usr/local/include.
4. Change <strong>Library Search Paths</strong> to usr/local/lib.
5. In the search field, remove your previous search and scroll down until you find the <strong>Linking</strong> section.
6. Double click on <strong>Other Linker Flags</strong>, click on the + button and add:

<pre><code>
  {% highlight c++ linenos %}
-lopencv_calib3d -lopencv_core -lopencv_features2d -lopencv_flann -lopencv_highgui -lopencv_imgcodecs -lopencv_imgproc -lopencv_ml -lopencv_objdetect -lopencv_photo -lopencv_shape -lopencv_stitching -lopencv_superres -lopencv_ts -lopencv_video -lopencv_videoio -lopencv_videostab'
  {% endhighlight %}
</code></pre>

<img src="https://lh3.googleusercontent.com/OCAoOs9NCwdy3kT-4EgkwVA7deLehMCZE2M_a3-tIqQjD6_fCpAfDQjozghRaC4HiN-IFh2RRQMswyHy8bfkp8NouIByLEBhpGT45A32ynPcSstcrguZP0ZqHbLLSK64bTX1n-fNAr9e4yW0shgclFeGPEVPF0e7IhWeYer_uk79d-TVPqQ_PzMbZClhR5-Qy-SQG7r0lyrd9QOzYqA9OXV9kQCcI79_qdHgPRWadCJPvAHsB_ImiqVdkT4cxSHNbg9e2U0gdLFP46sotryRLse-Joo0yhOH_To2wWtARohUf4oAjexPlGSfGhqXQtiMgYaZCBFTmprtlbtwmkOvRabtgmYaGQVKcKq5f5YAPgNQirLNNgvuZvhjiSdkjy679uxK1uKpHnRMLosVZg_8D3RMHhO6dzx4Fz2iFLqf8mHfN36WHIUYqz60o7EkKsWrVJhPQ1U_GurMs7AqzqFKrjIJAcDPoSqSebWb0FArL650VZSlcgQVWtrP9I6OGL5Txuk3dhPZDZbHtJG2OYsTeW3RFhop1BMVxujV=w2558-h1510-no">

All of the previous steps will allow Xcode to know where to look for when you import a library in your code.

Finally, go to your Xcode preferences, select <strong>Locations Tab</strong>, click <strong>Advanced</strong> and change the location button from <strong><em>Unique</em></strong> to <strong><em>Legacy</em></strong>.

##Writing our first program.

Click on 'File > Add Files to ...' and select the image processing library <em>(libopencv_imgproc.3.0.0.dylib)</em>, the core library <em>(libopencv_core.3.0.0.dylib)</em> and the gui library <em>(libopencv_highgui.3.0.0.dylib)</em>.

Now we need to import them in our main.cpp file and write the rest of the code to open a basic webcam window.

<pre><code>
    {% highlight c++ linenos %}
#include <iostream>
#include "opencv2/imgproc.hpp"
#include "opencv2/core.hpp"
#include "opencv2/highgui.hpp"

using namespace cv;

int main(){

    VideoCapture cap(0);

    while(true){
        Mat Webcam;
        cap.read(Webcam);
        imshow("Webcam", Webcam);
    }

}
  {% endhighlight %}
</code></pre>

<img src="https://lh3.googleusercontent.com/d9ml--5VqHfjyX1OhIO8FuR_6q3ScQDdPqrhh9yqDjeDimNXv3AzYKvTGLsUxB2FMtbwIguSlEd1AlFGnA1JfBwTDN2-A72JImwuw1VfOGqwPlo10H0i63ghFE7zfR2QJhSaaWl7T-s28PtMUGNJ3yJE677SfnqRcsEuRXJO1BMqAS6_W6Ytvh_UfYHXfpuTuuJJw3EFNhYZAUSpIdXeLBQC5PjwyFORQ1giH80h8b3_4sjJR6ek_wYUK_lp9mGuaYGvk5JobSdECpC3I7MgEujCcy11h15ocl5OK7iE7nErGsQvGb5qubkwd2XtwQUVOOUzKLrvGgVW3c850w2iS8QXuiSFFxl6exFvPUlpW6istJMtNFQIMPL2iXFxZDPQW1C8b-Us9bAgub5VRvMN4GumvfNJofmw7vpl8iOCHM2tVfvGuI9tXHHvqGTphp-iZraWjc10ujPxp3vPEeg8h_alfa6AWNmCsh5LsVhr6-PpvSke72XnCe06PWUtgTPIpw5syK86yFW-Nhbqc6rH4o7G3NPJ3XNpCbuO=w1480-h676-no">

You project should build successfully and a webcam window should open.

You're done!


