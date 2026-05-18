#  Experimental verification of PSK and QPSK
# Aim
Write a simple Python program for the modulation and demodulation of PSK and QPSK.
# Tools required
- Python
- Google Colab
- numpy script
# Theory
PSK (Phase Shift Keying):
Phase Shift Keying (PSK) is a digital modulation technique in which the phase of a carrier signal is changed according to the digital input data. It is used to transmit binary information efficiently over communication channels. In Binary PSK (BPSK), two different phase states such as 0° and 180° represent binary 0 and 1. PSK provides better noise immunity compared to amplitude modulation techniques. It is widely used in wireless communication systems and satellite communication. The bandwidth efficiency of PSK is relatively high.

QPSK (Quadrature Phase Shift Keying):
Quadrature Phase Shift Keying (QPSK) is an advanced form of PSK where four different phase shifts are used to represent data symbols. Each symbol carries two bits of information, increasing the data transmission rate. The phase values commonly used are 45°, 135°, 225°, and 315°. QPSK improves bandwidth efficiency compared to BPSK while maintaining good noise performance. It is commonly used in digital television, cellular communication, and satellite systems. QPSK allows faster transmission without requiring additional bandwidth.
# PSK Program
```python
import numpy as np
import matplotlib.pyplot as plt
from scipy.signal import butter, lfilter
# Butterworth low-pass filter for demodulation
def butter_lowpass_filter(data, cutoff, fs, order=5):
    nyquist = 0.5 * fs
    normal_cutoff = cutoff / nyquist
    b, a = butter(order, normal_cutoff, btype='low', analog=False)
    return lfilter(b, a, data)
# Parameters
fs = 1000                # Sampling frequency
f_carrier = 50           # Carrier frequency
bit_rate = 10            # Data rate (bits per second)
T = 1                    # Total time duration in seconds
t = np.linspace(0, T, int(fs * T), endpoint=False)
# Message signal (binary data)
bits = np.random.randint(0, 2, bit_rate)
bit_duration = fs // bit_rate
message_signal = np.repeat(bits, bit_duration)
# PSK Modulation (0 -> 0 phase, 1 -> 180° phase shift)
carrier = np.sin(2 * np.pi * f_carrier * t)
psk_signal = np.sin(2 * np.pi * f_carrier * t + np.pi * message_signal)
# PSK Demodulation
demodulated = psk_signal * carrier
filtered_signal = butter_lowpass_filter(demodulated, f_carrier, fs)
decoded_bits = (filtered_signal[::bit_duration] < 0).astype(int)
# Plotting
plt.figure(figsize=(12, 8))
plt.subplot(4, 1, 1)
plt.plot(t, message_signal, label='Message Signal (Binary)', color='b')
plt.title('Message Signal')
plt.ylabel('Amplitude')
plt.grid(True)
plt.subplot(4, 1, 2)
plt.plot(t, carrier, label='Carrier Signal', color='g')
plt.title('Carrier Signal')
plt.ylabel('Amplitude')
plt.grid(True)
plt.subplot(4, 1, 3)
plt.plot(t, psk_signal, label='PSK Modulated Signal', color='r')
plt.title('PSK Modulated Signal')
plt.ylabel('Amplitude')
plt.grid(True)
plt.subplot(4, 1, 4)
plt.step(np.arange(len(decoded_bits)), decoded_bits, label='Decoded Bits', color='r', marker='x')
plt.title('Decoded Bits')
plt.xlabel('Time')
plt.ylabel('Bit Value')
plt.grid(True)
plt.legend()
plt.tight_layout()
plt.show()
```
# Output Waveform
<img width="1190" height="790" alt="psk" src="https://github.com/user-attachments/assets/a611c76d-542f-4ceb-af6b-523979b9ba3e" />

# QPSK Program
```python
import numpy as np
import matplotlib.pyplot as plt
#Parameters
fs = 1000
# Sampling frequency
fc = 10
# Carrier frequency
T = 1
#Total duration
t = np.arange(0, T, 1/fs)
#Input bit pairs
bits = np.array([1,0, 1,1, 1,1, 1,0])
#10 11 11 10
symbols = bits.reshape(-1, 2)
symbol_samples = len(t) // len(symbols)
# QPSK Modulation (I-Q method)
qpsk = np.zeros(len(t))
for i, pair in enumerate(symbols):
    I = 1 if pair[0] == 1 else -1
    Q = 1 if pair[1]== 1 else -1
    ts = t[i*symbol_samples:(i+1)*symbol_samples]
    qpsk[i*symbol_samples: (i+1)*symbol_samples] = \
    I*np.cos(2*np.pi*fc*ts) + Q*np.sin(2*np.pi*fc*ts)
# Demodulation
decoded = []
for i in range(len(symbols)):
    ts = t[i*symbol_samples: (i+1)*symbol_samples]
    segment = qpsk[i*symbol_samples: (i+1)*symbol_samples] # Changed (1+1) to (i+1)
    I_demod = np.sum(segment * np.cos(2*np.pi*fc*ts))
    Q_demod = np.sum(segment * np.sin(2*np.pi*fc*ts))
    decoded.append(1 if I_demod > 0 else 0) # Assumed > 0 for decision
    decoded.append(1 if Q_demod > 0 else 0) # Changed Ø to 0
# Plot
plt.figure(figsize=(10,8))
plt.suptitle("NAME: NAVEEN K \nREG NO: 212223060184",
fontsize=12, fontweight='bold')
plt.subplot(3,1,1)
plt.step(range(len(bits)), bits, where='mid')
plt.title("Input Binary Data")
plt.ylim(-0.5,1.5)
plt.grid(True)
plt.subplot(3,1,2)
plt.plot(t, qpsk)
plt.title("QPSK Modulated Signal")
plt.grid(True)
plt.subplot(3,1,3)
plt.step(range(len(decoded)), decoded, where='mid')
plt.title("Demodulated Output")
plt.ylim(-0.5,1.5)
plt.grid(True)
plt.tight_layout(rect=[0,0,1,0.93])
plt.show()
```
# Output Waveform
<img width="989" height="789" alt="qpsk" src="https://github.com/user-attachments/assets/edd792b9-8977-43ce-8ad0-16b7c51a3cdd" />


# Result
The experiment of modulation and demodulation of Phase Key Shifting and Quadriphase Key Shifting was successfully executed.
