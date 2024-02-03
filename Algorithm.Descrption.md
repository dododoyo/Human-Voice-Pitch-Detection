# Human Voice Pitch Detection 


## Instructions

1. **Locate the Audio File**: Find the .wav audio file you want to analyze on your computer and note its full path. The full path includes the drive, any folders, and the file name. For example, on Windows, it might look like `C:\Users\YourName\Documents\myAudioFile.wav`.

2. **Open MATLAB**: Start the MATLAB application on your computer. You'll see a command window where you can type commands and scripts.

3. **Read the Audio File**: In the MATLAB command window, type the following command to read the audio file:

    ```matlab
    [X, F_s] = audioread('full_path_to_your_file');
    ```

    Replace `'full_path_to_your_file'` with the full path to your .wav file. Make sure to keep the quotation marks around the file path. For example:

    ```matlab
    [X, F_s] = audioread('C:\Users\YourName\Documents\myAudioFile.wav');
    ```

    This command reads the audio data from the file into the variable `X` and the sample rate into the variable `F_s`.

4. **Proceed with the Analysis**: You can now proceed with the rest of the code for pitch detection, using the `X` and `F_s` variables as needed.

Remember to replace `'full_path_to_your_file'` with the actual path to your .wav file. If the file is in the same directory as your MATLAB script, you can just use the file name.
## Explanation
1. Reading the audio file:
   - The code starts by reading an audio file using the `audioread` function.From the file path specified.
   - The audio file is read into two variables: `X` and `F_s`.
   - X — Audio data
    Audio data in the file, returned as an m-by-n 
    matrix, where m is the number of audio samples read
    and n is the number of audio channels in the file.

    If you do not specify dataType, or dataType is 
    'double'(64 bit per sample), then y is of type 
    double, and matrix elements are normalized values between −1.0 and 1.0.

    If dataType is 'native', then y can be one of several MATLAB data types, depending on the file format and the BitsPerSample value of the input file. Call audioinfo to determine the BitsPerSample value of the file.

2. Converting the audio signal to double:
   - The audio signal `X` is converted to double precision using the [`double`](https://www.mathworks.com/help/symbolic/sym.double.html) function. This is done to ensure that the signal can be processed accurately.

3. Setting up parameters:
   - The code sets up various parameters for pitch detection.
   - `N` represents the frame length in milliseconds. In the context of audio processing, a frame refers to a short segment of the audio signal. The frame length is the duration of this segment, usually measured in milliseconds (ms). 
   - `number_of_samples = frame_length_in_seconds * sampling_rate`
   - When processing audio signals, it's common to divide the signal into overlapping frames and process each frame independently. This is based on the assumption that the audio signal is stationary (i.e., its properties do not change) over short periods of time.

   - The frame length is a critical parameter in many audio processing algorithms. It must be chosen carefully to balance between time resolution and frequency resolution. A shorter frame length allows for better time resolution (i.e., detecting changes in the signal over time), but worse frequency resolution (i.e., distinguishing between different frequencies). Conversely, a longer frame length provides better frequency resolution but worse time resolution.
   - `frame_size` is calculated based on the frame length and the sampling frequency.
   
   - `X_size` represents the length of the audio signal.

4. Calculating the number of windows:
   - In audio signal processing, a window refers to a subset of the audio signal that you're currently processing. It's a small chunk of the overall signal. The term "window" is used because it's as if you're looking at the signal through a small window that only shows a part of the signal at a time.

   - The window size is typically the same as the frame size. When you "window" a signal, you're essentially dividing the signal into frames. Each frame is then multiplied by a window function to minimize the discontinuities at the beginning and end of the frame. This process is known as windowing.

   - The overlap between windows refers to how much each window overlaps with the previous one. For example, if the overlap is 0.5 (or 50%), then each window starts halfway through the previous window. This is done to ensure that the signal is fully represented, even at the edges of each window.

   - In the context of audio signal processing, a window with overlap refers to the method of dividing the audio signal into smaller segments or "windows" that overlap with each other.

   - The overlap is usually specified as a fraction of the window size. For example, if the overlap is 0.5 (or 50%), then each window starts halfway through the previous window. This means that half of the data in each window is shared with the previous window.

   Overlapping is used in audio signal processing for a few key reasons:

1. **Continuity**: Overlapping ensures that there is continuity between frames. Without overlap, the end of one frame and the start of the next may not align perfectly, leading to discontinuities in the processed signal.

2. **Windowing artifacts**: When a window function (like a Hamming window or a Hanning window) is applied to a frame, it tends to de-emphasize the edges of the frame. If there's no overlap between frames, this can result in a loss of information at the edges. Overlapping frames mitigates this issue.

3. **Resolution**: Overlapping can increase the time resolution of the analysis. With more overlap, the same portion of the signal is analyzed multiple times with slightly different starting points, which can lead to a more detailed representation of the signal.

4. **Frequency leakage**: Overlapping can also help to reduce spectral leakage, which is a common issue in Fourier-based methods. Spectral leakage occurs when the frequency content of a signal is spread out over multiple frequency bins, causing a loss of precision in the frequency domain representation. Overlapping can help to mitigate this issue.

In summary, overlapping is a technique used to improve the accuracy and resolution of audio signal processing tasks.


   -Here's a simple illustration of how windows might overlap with 50% overlap:

   ```
   |---- Window 1 ----|
         |---- Window 2 ----|
               |---- Window 3 ----|
   ```

   -Each dash represents a sample in the signal, and each window includes half of the samples from the previous window.

   - The code calculates the number of windows based on the frame size, overlap, and the length of the audio signal.
  
   - The number of windows is calculated using the formula: `(X_size - frame_size) / (frame_size * overlap) - 1`.
   - The result is rounded up using the `ceil` function.


1. Pitch detection using center clipping:
   ### Center Clipping 
   - Center clipping is a non-linear process applied to an audio signal to enhance the periodic components, which are often related to the fundamental frequency or pitch of the signal. This is particularly useful in speech signal processing for pitch detection.

   - The center clipping process involves setting all signal values that fall within a certain range around zero to zero. This range is defined by a clipping level, which is typically a proportion of the maximum amplitude of the signal.

There are two types of center clipping mentioned in our code:

1. Regular center clipping: This is the basic form of center clipping where all values within the clipping level are set to zero.

2. Three-level center clipping: This is a variant of center clipping where the signal is divided into three levels: positive, negative, and zero. The positive and negative levels are determined by the clipping level, and all values within these levels are set to a constant positive or negative value, respectively. All other values are set to zero.

The purpose of center clipping is to suppress the non-periodic components of the signal (like noise) and enhance the periodic components (like pitch). This makes it easier to detect the pitch of the signal in the subsequent processing steps.

   - The code calls the `find_pitch_freq` function twice to detect the pitch using different center clipping methods.
   - The first call uses `type_of_centerclipping = 1`, which corresponds to the 3-level center clipping method.
   - The second call uses `type_of_centerclipping = 2`, which corresponds to the regular center clipping method.
   - The `find_pitch_freq` function takes the audio signal `X`, the type of center clipping, the number of windows, frame size, overlap, and `N` as input.
   - Inside the `find_pitch_freq` function, the code performs the following steps:
     - It applies a rectangular window to each frame of the audio signal.
     - It calculates the energy and zero-crossing rate of each frame.
     - Depending on the type of center clipping, it applies the corresponding center clipping method (`center_cliping_3level` or `center_cliping`).
     - It calculates the autocorrelation of the center-clipped signal.
     - If the energy of a frame is below a certain threshold (500000 in this case), the autocorrelation is set to all zeros.
     - Finally, it finds the locations of the peaks in the autocorrelation function and calculates the pitch candidates based on these locations.

2. Calculating pitch frequency:
   - The code calls the `calcute_fc` function twice to calculate the pitch frequency for each center clipping method.
   - The `calcute_fc` function takes the pitch candidates, the number of windows, and a flag `islvl` as input.
   - Inside the `calcute_fc` function, the code performs the following steps:
     - It removes pitch candidates that are outside the range of 75 Hz to 200 Hz.
     - It applies smoothing to the remaining pitch candidates using the `smooth2a` function.
     - It extracts the first valid pitch candidate for each window and stores it in `pointsY`.
     - It assigns the window index to `pointsX`.

3. Plotting the results:
   - The code uses the `subplot` function to create a 4x1 grid of subplots.
   - It plots the pitch candidates and pitch frequencies for both center clipping methods.
   - The `all_points` function is used to extract all valid pitch candidates for plotting.
   - The `plot` function is used to plot the data points, and various formatting options are applied to the plots.

4. Helper functions:
   - The code includes several helper functions to perform specific tasks:
     - `all_points` function extracts all valid pitch candidates from the input array.
     - `calcute_fc` function calculates the pitch frequency based on the pitch candidates.
     - `find_pitch_freq` function detects the pitch candidates using center clipping.
     - `find_locs` function finds the locations of the peaks in the autocorrelation function.
     - `center_cliping` and `center_cliping_3level` functions perform center clipping on the input signal.
     - `energy` function calculates the energy of a signal.
     - `zero_crossing` function calculates the zero-crossing rate of a signal.
     - `autocorelation` function calculates the autocorrelation of a signal.
     - `smooth2a` function performs 2D smoothing on a matrix.

   Overall, this code uses a combination of center clipping, autocorrelation, and peak detection to detect the pitch of human voice in an audio signal. The pitch candidates are then processed to extract the most likely pitch frequency for each window, which is then plotted for visualization.

  ### all points
      - The provided MATLAB function `all_points` takes a 2D array as input and returns two 1D arrays, `pointX` and `pointY`. The input array is expected to have 5 columns, and each element in the array represents a pitch candidate.

      - The function first initializes `pointX` and `pointY` as 1D arrays filled with `NaN` values. The length of these arrays is five times the length of the input array, which suggests that up to five pitch candidates are expected for each frame of the audio signal.

      - The function then iterates over the input array. For each element in the array, it checks if the value is between 75 and 200. This range likely corresponds to the typical range of human voice pitch in Hz. If the value is within this range, the function adds the index (or position) to `pointX` and the value (or pitch candidate) to `pointY`.

      - In the context of the whole code, this function is likely used to gather all the valid pitch candidates after the pitch detection algorithm has been applied. These pitch candidates can then be used for further analysis or visualization.
  ### calculate_fc

  The provided MATLAB function `calcute_fc` takes three inputs: `final_candidate`, `num_of_window`, and `islvl`. The `final_candidate` is a 2D array where each row represents a window of the audio signal and each column represents a pitch candidate. The `num_of_window` is the total number of windows that the audio signal is divided into. The `islvl` is a parameter that affects the smoothing of the final candidates.

   The function first initializes `pointsX` and `pointsY` as 1D arrays filled with `NaN` values. The length of these arrays is equal to the number of windows.

   Next, the function iterates over the `final_candidate` array. For each pitch candidate, it checks if the value is outside the range of 75 to 200. If it is, the function sets the value to `NaN`. This step is likely used to remove pitch candidates that fall outside the typical range of human voice pitch.

   The function then applies a smoothing function to the `final_candidate` array. The smoothing function takes two parameters that determine the degree of smoothing in the row and column directions. These parameters are calculated based on the `islvl` input.

   After smoothing, the function sets the `pointsX` array to the indices of the windows. This is done to prepare for the next step, where the function selects a single pitch candidate for each window.

   Finally, the function iterates over the smoothed `final_candidate` array again. For each window, it selects the first pitch candidate that falls within the range of 75 to 200 and assigns it to the corresponding element in `pointsY`. If no such candidate is found, the element in `pointsY` remains `NaN`.

   In the context of the whole code, this function is likely used to process the pitch candidates after the pitch detection algorithm has been applied. The output arrays `pointsX` and `pointsY` can then be used for further analysis or visualization.

### find_pitch_freq 

The provided MATLAB function `find_pitch_freq` is used to find the pitch frequency candidates in an audio signal. It takes six inputs: `X` (the audio signal), `type_of_centerclipping` (the type of center clipping to apply), `num_of_window` (the number of windows to divide the signal into), `frame_size` (the size of each frame), `overlap` (the overlap between frames), and `N` (the sampling rate).

The function first creates a rectangular window of size `frame_size` using the `rectwin` function. It then initializes two empty arrays, `acf` and `Energy`, to store the autocorrelation function and energy of each frame, respectively.

The function then iterates over the number of windows. For each window, it multiplies the corresponding segment of the audio signal by the rectangular window to apply windowing. It then calculates the energy of the windowed signal and appends it to the `Energy` array.

Next, it applies center clipping to the windowed signal. The type of center clipping applied depends on the `type_of_centerclipping` input. The clipped signal is then passed to the `autocorelation` function to calculate its autocorrelation.

If the energy of the windowed signal is less than 500000, a row of zeros is appended to the `acf` array. Otherwise, the autocorrelation of the clipped signal is appended.

After processing all the windows, the function iterates over the `acf` array again. For each row in `acf`, it finds the locations of the local maxima using the `find_locs` function. These locations are then converted to frequencies and stored as pitch candidates.

If a row in `acf` has one or fewer local maxima, all the pitch candidates for that window are set to zero.

In the context of the whole code, this function is likely used to perform pitch detection on the audio signal. The output is a 2D array where each row represents a window and each column represents a pitch candidate.


### find_locs
  The provided MATLAB function `find_locs` is used to find the locations of the local maxima in a 1D array `y`. These locations are typically used to identify the pitch candidates in an audio signal.

The function first applies a smoothing operation to `y` three times in a row. This is done to reduce the noise in the signal and make the peaks more prominent.

Next, it uses the `findpeaks` function to find the local maxima (or peaks) in the smoothed signal. The `findpeaks` function returns two arrays: `pks`, which contains the values of the peaks, and `locs`, which contains the locations of the peaks.

The function then initializes an empty array `new_locs` to store the locations of the selected peaks. It also calculates the `minimum` of 5 and the number of peaks. This is done to limit the number of peaks that are selected to a maximum of 5.

The function then enters a loop that runs for `minimum` iterations. In each iteration, it finds the maximum value in `pks` and the corresponding index. If there is only one maximum, it adds the corresponding location to `new_locs` and removes the maximum from `pks` and `locs`. If there are two maxima, it adds both locations to `new_locs` and removes both maxima from `pks` and `locs`.

Finally, the function sets `locs` to `new_locs` and returns it. This means that the output of the function is the locations of the `minimum` highest peaks in `y`.

In the context of the whole code, this function is likely used to select the most prominent pitch candidates from the autocorrelation of a windowed audio signal.

### center clipping

The provided MATLAB function `center_cliping` performs center clipping on an input signal `y`. Center clipping is a technique used in signal processing to suppress low amplitude signals, which can help to reduce noise and enhance the prominent features of the signal.

The function first initializes `output` as a zero array of the same length as `y`. This will be the output of the function, representing the center-clipped version of `y`.

Next, it calculates the maximum absolute value of `y` and stores it in `maxvalue`. This value is used to determine the threshold for clipping.

The function then sets a coefficient `coef` to 0.2. This coefficient determines the threshold for clipping. Any values in `y` that are less than `coef * maxvalue` in absolute value will be clipped to zero.

The function then iterates over `y`. For each element in `y`, it checks if the value is greater than or equal to `coef * maxvalue`. If it is, the function subtracts `coef * maxvalue` from the value and assigns the result to the corresponding element in `output`. If the value is less than or equal to `-coef * maxvalue`, the function adds `coef * maxvalue` to the value and assigns the result to `output`.

In the context of the whole code, this function is likely used to preprocess the audio signal before applying a pitch detection algorithm. By suppressing low amplitude signals, center clipping can make the pitch detection more robust to noise.

### center clipping  3level

The provided MATLAB function `center_cliping_3level` performs a three-level center clipping on an input signal `y`. This is a type of signal processing technique used to suppress low amplitude signals and enhance the prominent features of the signal.

The function first initializes `output` as a zero array of the same length as `y`. This will be the output of the function, representing the three-level center-clipped version of `y`.

Next, it calculates the maximum absolute value of `y` and stores it in `maxvalue`. This value is used to determine the threshold for clipping.

The function then sets a coefficient `coef` to 0.2. This coefficient determines the threshold for clipping. Any values in `y` that are less than `coef * maxvalue` in absolute value will be clipped to zero.

The function then iterates over `y`. For each element in `y`, it checks if the value is greater than or equal to `coef * maxvalue`. If it is, the function sets the corresponding element in `output` to 1. If the value is less than or equal to `-coef * maxvalue`, the function sets the corresponding element in `output` to -1.

In the context of the whole code, this function is likely used to preprocess the audio signal before applying a pitch detection algorithm. By suppressing low amplitude signals and converting the remaining signals to a three-level representation, this form of center clipping can make the pitch detection more robust to noise and easier to analyze.

### energy 

The provided MATLAB function `energy` calculates the energy of an input signal `y`. In the context of signal processing, the energy of a signal is the sum of the squares of its amplitude values. It's a measure of the signal's power over time.

The function first initializes a variable `e` to 0. This variable will be used to accumulate the energy of the signal.

The function then enters a loop that iterates over each element in `y`. For each element, it squares the value and adds it to `e`.

Finally, the function returns `e`, which represents the total energy of the signal `y`.

In the context of the whole code, this function is likely used to analyze the power characteristics of an audio signal. For example, it could be used to identify silent or quiet parts of the signal (which would have low energy) or to normalize the signal based on its energy.

### zero crossing

The provided MATLAB function `zero_crossing` calculates the zero-crossing rate of an input signal `y`. The zero-crossing rate is a measure of the number of times a signal changes sign, i.e., goes from positive to negative or vice versa. This is often used in signal processing and machine learning to analyze the frequency characteristics of a signal or to detect events.

The function first initializes a variable `z` to 0. This variable will be used to accumulate the zero-crossing rate.

The function then enters a loop that starts from the second element in `y` and iterates to the end of `y`. For each element, it calculates the absolute difference between the sign of the current element and the sign of the previous element, divides it by 2, and adds it to `z`. The division by 2 is done because the `sign` function returns -1, 0, or 1, so the absolute difference between the signs of two consecutive elements that cross zero will be 2.

Finally, the function normalizes `z` by dividing it by twice the length of `y` and returns it. The normalization is done to make the zero-crossing rate independent of the length of the signal.

In the context of the whole code, this function is likely used to analyze the frequency characteristics of an audio signal. For example, it could be used to detect the presence of high-frequency noise (which would result in a high zero-crossing rate) or to classify the signal based on its zero-crossing rate.

### autocorelation

The provided MATLAB function `autocorelation` calculates the autocorrelation of an input signal `y`. Autocorrelation is a mathematical tool used in signal processing to analyze the similarity of a signal with a delayed version of itself over successive time intervals. It's often used to detect repeating patterns or periodic signals in data.

The function first initializes `auto_cor` as a zero array of the same length as `y`. This will be the output of the function, representing the autocorrelation of `y`.

The function then enters a nested loop. The outer loop iterates over each element in `y`, treating each element as a different delay for the autocorrelation. The inner loop starts from the current delay and iterates to the end of `y`. For each delay, it calculates the product of the current element and the element at the current delay, and accumulates these products in `sum`.

After calculating the sum for each delay, the function assigns the sum to the corresponding element in `auto_cor`.

Finally, the function returns `auto_cor`, which represents the autocorrelation of `y`.

In the context of the whole code, this function is likely used to analyze the frequency characteristics of an audio signal. For example, it could be used to detect the pitch of the signal, as the autocorrelation of a periodic signal will also be periodic with the same period.

### smooth2a

The provided MATLAB function `smooth2a` performs a 2D smoothing operation on an input matrix `matrixIn` using a boxcar averaging method. This method is often used in digital signal processing to reduce noise or to approximate the local mean of the signal.

The function takes three arguments: `matrixIn`, `Nr`, and `Nc`. `matrixIn` is the input matrix to be smoothed. `Nr` and `Nc` are the sizes of the smoothing window in the row and column dimensions, respectively. If `Nc` is not provided, it is set to the same value as `Nr`.

The function first checks if the necessary arguments are provided and if `Nr` and `Nc` are scalars. If not, it throws an error.

Next, it creates two sparse diagonal matrices `eL` and `eR` with ones on the diagonals extending from `-N(1)` to `N(1)` and `-N(2)` to `N(2)`, respectively. These matrices are used to perform the boxcar averaging.

The function then identifies the NaN values in `matrixIn` and replaces them with zeros. It also creates a normalization matrix `nrmlize` that counts the number of non-NaN values in the smoothing window for each element in `matrixIn`.

The function then performs the boxcar averaging by multiplying `matrixIn` with `eL` and `eR`. It also normalizes the result by dividing it by `nrmlize`.

Finally, the function returns the smoothed matrix `matrixOut`.

In the context of the whole code, this function is likely used to preprocess an image or a 2D signal before further analysis. By smoothing the data, it can reduce noise and make the signal's features more prominent.


[autoread function documentation](https://www.mathworks.com/help/matlab/ref/audioread.html)

[double function documentation](https://www.mathworks.com/help/symbolic/sym.double.html)