# Human Voice Pitch Ditection


##   Introduction


In this project, we embark on a thrilling journey to uncover the secrets of voice pitch. We will
delve into the science behind sound, dissecting how the subtle vibrations of vocal cords
translate into the symphony of spoken language. 

We will explore existing methods of pitch
detection, each playing a unique note in the orchestra of understanding how pitch varies and
shapes our spoken narratives.
But why seek to unlock the secrets of pitch?
The answer lies in its vast potential. Imagine applications that can analyze vocal health,
enhance music analysis tools, or even guide and perfect the art of singing. By unveiling the
melody within voice, we stand to unlock a treasure trove of insights and possibilities in music,
communication, and beyond.

Throughout this project, we will:

 - Explore the landscape of pitch detection: Charting the diverse terrain of existing methods,each with its own strengths and limitations.
 - Craft our own musical algorithm: Devising a pitch detection approach that resonates with our chosen methodology and desired results.
 - Translate theory into practice: Transforming our chosen algorithm into a symphony of MATLAB code, orchestrated to extract pitch from the audio canvas.
 - Unleash the hidden melodies: Analyzing the results, evaluating the accuracy and limitations of our creation, and refining our understanding of voice pitch detection.
By the end of this project, we aim to not only unravel the mysteries of voice pitch but also compose a compelling narrative of technological exploration and discovery. 

## Background

### The Human Voice

- To appreciate the intricacies of voice pitch detection, we must first peel back the curtain on the remarkable instrument itself - the human voice. Imagine a hidden chamber within your throat, the larynx, housing a pair of delicate membranes aptly named vocal cords. These vibrates like microscopic strings, weaving the tapestry of your spoken words and melodious songs. 

- But what orchestrates this symphony of sound? The conductor, it turns out, is the interplay between airflow
from your lungs and the tension and length of your vocal cords. When you breathe in, your vocal cords open wide, allowing air to rush through freely. But when you speak or sing, these membranes come together, partially blocking the airflow. This creates a build-up of pressure, like a dam holding back a rushing river.

- As the pressure reaches a critical point, your vocal cords snap apart, releasing a burst of compressed air. This explosive release is the foundation of sound production.

- But here's the magic: the frequency of these tiny explosions, measured in Hertz (Hz), dictates the perceived pitch of your voice.

### Possible Voice Pitch Detection Algorithms

#### Autocorrelation

This is generally considered the simplest of the three algorithms. It involves calculating the similarity of
the speech signal to itself at different time lags, which can be done efficiently with the Fast Fourier
Transform (FFT). While slightly more complex than Zero-Crossing, it provides better accuracy and
robustness.

#### AMDF (Average Magnitude Difference Function)
While still relatively simple, AMDF is slightly more complex than Autocorrelation. It calculates the
average difference between the absolute values of the signal at different time lags. This can be a bit
faster than Autocorrelation but might be less robust to noise.

#### Cepstrum

This is the most complex of the three algorithms mentioned. It involves taking the log magnitude
spectrum of the signal, then taking the inverse Fourier transform to obtain the cepstral coefficients.
This process extracts information about the pitch and formants from the signal.While it can be very
 accurate and robust, it also requires more computational resources and is less straightforward to implement compared to the other two.

### Algorithms Comparison


| Algorithm | Complexity | Accuracy | Robustness | Ease of Implementation |
| --------- | ---------- | -------- | ---------- | ---------------------- |
| Autocorrelation | Low  |  Medium  |    Good    |       High             |
| AMDF     | Low -Medium |  Medium  |  Moderate  |       Medium           |
| Cepstrum        | High |  High    | Excellent  |       Low              |


Ultimately, the best choice for implementation depends on specific needs and priorities. We
considered factors like:

- Required accuracy: If you need highly accurate pitch estimation, Cepstrum might be
the best choice, despite its complexity.
- Real-time requirements: If you need to do pitch detection in real-time, Autocorrelation or AMDF might be better options due to their lower computational cost.
- Programming experience: If you're new to signal processing, Autocorrelation or AMDF might be easier to learn and implement.

## Methodology: Autocorrelation Pitch Detection

###  Underlying Concepts and Mathematical Principles:

Autocorrelation measures the similarity of a signal to itself shifted by different time lags.
For a voiced speech signal with fundamental frequency (F0), the autocorrelation function
(ACF) will exhibit prominent peaks at multiples of F0, indicating repeating patterns.

### Key Parameters and Effects:
- Window size: A smaller window focuses on local pitch variations but is less robust to noise. A larger window provides smoother pitch estimates but may miss rapid
changes.
- Smoothing: Techniques like peak picking or median filtering can help refine the detected pitch values.
- Minimum threshold: Setting a minimum peak amplitude threshold avoids identifying spurious peaks, but it might miss weak or noisy pitches.

### Justification for Choosing Autocorrelation:

-  Simplicity: Autocorrelation is computationally efficient and relatively easy to implement compared to other methods like Cepstrum.
  
- Accuracy: It provides good accuracy for voiced speech with stable pitch.

- Robustness: Autocorrelation is robust to moderate noise and formant structure
compared to AMDF.

##  Implementation in MATLAB

### Tools and Resources:

- MATLAB version: Recommend using a recent version like R2022b or later for compatibility with the suggested toolboxes.
Toolboxes:
-  Signal Processing Toolbox: Provides essential functions for audio processing,
filtering, and windowing.
- Audio Toolbox (optional): For advanced audio analysis features like spectral analysis and voicing detection (if needed).
###  Code Structure Overview:

-  Audio Loading:
   - Load the audio file into the MATLAB workspace.
    - Convert the audio data to double-precision format for numerical accuracy.
  
- Frame Segmentation:
  - Divide the audio signal into overlapping frames of a specified duration (e.g.,40 milliseconds).
  - Apply a rectangular window to each frame to reduce spectral leakage.
- Center Clipping:
  - Apply either regular center clipping or 3-level center clipping to each frame.
  - Regular center clipping sets a threshold based on a percentage of the maximum amplitude and clips values above and below the threshold.
  - 3-level center clipping sets a threshold and clips values to 1, 0, or -1, depending on their magnitude relative to the threshold.
-  Autocorrelation Calculation:
   - Calculate the autocorrelation of each clipped frame to identify periodic patterns.
- Pitch Candidate Extraction:
  - Find prominent peaks in the autocorrelation function, representing potential pitch candidates.
  
  - Convert the peak locations to pitch frequencies in Hertz.
- Candidate Filtering and Smoothing:
  - Filter out pitch candidates outside a plausible range (e.g., 75-200 Hz).

  - Smooth the remaining pitch candidates using a 2D smoothing technique to reduce noise and abrupt variations.
- Visualization:
  - Plot the pitch candidates and the estimated pitch contour for each center clipping method.
  - This allows for visual comparison of the results and assessment of their quality.


### Custom Functions:



## Results and Discussion

### Result for a Test Voice

![Result 1](/images/result1.png)
Figure 1: Pitch Frequency Center Clipping

![Result 2](/images/result2.png)
Figure 2: Pitch Candidate Center Clipping

![Result 3](/images/result3.png)
Figure 3: Pitch Frequency 3level Center Clipping

![Result 4](/images/result4.png)
Figure 4: Pitch Candidate 3level Center Clipping


### Discussion
Analyzing the accuracy and limitations of the pitch detection method implemented in the MATLAB code
requires a deep understanding of the specific algorithm and the context in which it's used. However, we
have given some general insights based on the information we observed.

#### Accuracy and Limitations:

The accuracy of the pitch detection method depends on several factors, including the quality of the input signal, the presence of noise, and the characteristics of the speech (e.g., speaker's gender, speaking rate).

Autocorrelation is a well-established method for pitch detection, but it can be sensitive to noise and may have difficulty distinguishing the fundamental frequency from its harmonics. 
Center clipping can help to reduce the effects of noise and enhance the periodic components of the signal, but
it can also distort the signal and cause errors in pitch detection, especially if the clipping level is not appropriately set.

#### Future Improvements:

1. Noise Reduction: Implementing a noise reduction algorithm before pitch detection could improve the
accuracy of the method.
2. Adaptive Clipping Level: Instead of using a fixed clipping level, an adaptive method could be used to
set the clipping level based on the characteristics of the signal.

## Conclusion

### Key Findings and Achievements

- Our MATLAB code provided implements a pitch detection algorithm using autocorrelation and center clipping methods. It processes an audio signal, applies center clipping (both regular and three-level), calculates the autocorrelation, and identifies pitch candidates. The code then smooths these pitch candidates to determine the final pitch frequency. 
- The results are visualized in four plots, showing the pitch candidates and detected pitch frequencies for both center clipping methods.
- This implementation demonstrates the feasibility of using autocorrelation and center clipping for pitch detection in MATLAB. It also provides a basis for comparing the effects of regular and three-level center clipping on pitch detection.

### Potential Sources of Errors
1. Noise: Noise in the input signal can distort the autocorrelation function and lead to errors in pitch detection.
2. Clipping Level: The level at which the signal is center clipped can significantly affect the results. If the
level is too high, important parts of the signal may be clipped, leading to errors in pitch detection.
3. Harmonics: The autocorrelation method can sometimes confuse the fundamental frequency with its harmonics, leading to errors in pitch detection.
   
### Significance and Potential Applications of Voice Pitch Detection

Voice pitch detection is a crucial component of many applications in speech processing and music technology. It's used in speech recognition systems to improve accuracy, in music software to detect the pitch of notes, in voice assistants to understand user commands, and in telecommunication to
improve the quality of voice transmission. 
It can also be used in health care for diagnosing and monitoring conditions that affect the voice, such as Parkinson's disease.

### Suggestions for Future Research:
Future research in this area could focus on improving the accuracy and robustness of pitch detection
algorithms, especially in noisy conditions. This could involve exploring more advanced methods for
pitch detection, such as machine learning-based methods, or developing new techniques for noise
reduction and signal enhancement. Another interesting direction could be the integration of pitch
detection with other speech processing techniques, such as speech recognition or speaker
identification, to create more sophisticated systems. For example, pitch information could be used to
improve the accuracy of speech recognition in multi-speaker environments.

## References

[WavSource](https://www.wavsource.com/people/people.htm)

[MATLAB Audioread function Documentation](https://www.mathworks.com/help/matlab/ref/audioread.html)

[MATLAB double function Documentation](https://www.mathworks.com/help/symbolic/sym.double.html)
