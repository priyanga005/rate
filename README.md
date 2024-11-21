import numpy as np
import matplotlib.pyplot as plt
from scipy import signal

# Create a sample signal (sine wave) for demonstration
fs = 1000  # Original sampling rate (Hz)
t = np.arange(0, 1, 1/fs)  # Time vector (1 second duration)
x = np.sin(2 * np.pi * 50 * t) + 0.5 * np.sin(2 * np.pi * 150 * t)  # Signal with two frequencies

# Rate conversion factors
L = 4  # Upsampling factor
M = 3  # Downsampling factor
R = L / M  # Rate conversion factor

# Step 1: Upsample by a factor of L (insert zeros between samples)
x_upsampled = np.zeros(len(x) * L)
x_upsampled[::L] = x  # Insert original samples at every Lth position

# Step 2: Low-pass filtering (to smooth the upsampled signal)
nyquist = fs / 2
cutoff = nyquist / L  # Cutoff frequency for the upsampled signal
order = 4  # Filter order
b, a = signal.butter(order, cutoff / nyquist, btype='low')  # Butterworth low-pass filter design
x_filtered = signal.filtfilt(b, a, x_upsampled)  # Apply filter to the upsampled signal

# Step 3: Downsample by a factor of M (select every Mth sample)
x_rate_converted = x_filtered[::M]

# Plotting the results
plt.figure(figsize=(12, 6))

# Original signal
plt.subplot(3, 1, 1)
plt.plot(t, x, label="Original Signal", color='b')
plt.title("Original Signal")
plt.xlabel("Time [seconds]")
plt.ylabel("Amplitude")
plt.grid(True)

# Upsampled and filtered signal
t_upsampled = np.arange(0, len(x_upsampled)) / (fs * L)  # Time vector for upsampled signal
plt.subplot(3, 1, 2)
plt.plot(t_upsampled, x_filtered, label="Upsampled and Filtered Signal", color='g')
plt.title("Upsampled and Filtered Signal")
plt.xlabel("Time [seconds]")
plt.ylabel("Amplitude")
plt.grid(True)

# Rate-converted signal (after downsampling)
t_rate_converted = np.arange(0, len(x_rate_converted)) * M / fs  # Time vector for rate-converted signal
plt.subplot(3, 1, 3)
plt.stem(t_rate_converted, x_rate_converted, label="Rate Converted Signal", basefmt=" ", use_line_collection=True)
plt.title(f"Rate Converted Signal (Factor R={R:.2f})")
plt.xlabel(
