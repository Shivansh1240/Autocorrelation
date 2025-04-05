# Autocorrelation
This is the code I used to find the autocorrelation of magnetic field data in three spatial directions
import numpy as np
import matplotlib.pyplot as plt


data = np.loadtxt('interpolated_data2(ULL_1995_B).txt')


Bx = data[:, 1]
By = data[:, 2]
Bz = data[:, 3]
n = len(Bx)


Bx_mean = np.mean(Bx)
By_mean = np.mean(By)
Bz_mean = np.mean(Bz)


def autocorrelation(arr, mean, n):
    def c(k):
        return np.mean([(arr[i] - mean) * (arr[i + k] - mean) for i in range(n - k)])
    def r(k):
        return c(k) / c(0)
    return [r(k) for k in range(min(1000, n))]


autocorrelation_Bx = autocorrelation(Bx, Bx_mean, n)
autocorrelation_By = autocorrelation(By, By_mean, n)
autocorrelation_Bz = autocorrelation(Bz, Bz_mean, n)
print(autocorrelation_Bx)

Autocorrelation_main=autocorrelation_Bx+autocorrelation_By+autocorrelation_Bz

plt.figure(figsize=(12, 6))
plt.plot(Autocorrelation_main, label='Autocorrelation of X')

# Calculate the initial value and threshold
initial_value_x = autocorrelation_Bx[0]
initial_value_y = autocorrelation_By[0]
initial_value_z = autocorrelation_Bz[0]

# Find the maximum of initial values
initial_value = max(initial_value_x, initial_value_y, initial_value_z)
threshold = initial_value / np.e

# Find the lag where any autocorrelation drops below the threshold
def find_lag_below_threshold(autocorrelation, threshold):
    return next((lag for lag, value in enumerate(autocorrelation) if value < threshold), None)

lag_when_drops_x = find_lag_below_threshold(autocorrelation_Bx, threshold)
lag_when_drops_y = find_lag_below_threshold(autocorrelation_By, threshold)
lag_when_drops_z = find_lag_below_threshold(autocorrelation_Bz, threshold)

# Plotting the autocorrelation
plt.figure(figsize=(12, 6))
plt.plot(autocorrelation_Bx, label='Autocorrelation of X')
plt.plot(autocorrelation_By, label='Autocorrelation of Y')
plt.plot(autocorrelation_Bz, label='Autocorrelation of Z')
plt.axhline(y=threshold, color='r', linestyle='--', label=f'1/e threshold ({threshold:.4f})')

if lag_when_drops_x is not None:
    plt.axvline(x=lag_when_drops_x, color='g', linestyle='--', label=f'Lag X at 1/e threshold ({lag_when_drops_x})')
if lag_when_drops_y is not None:
    plt.axvline(x=lag_when_drops_y, color='b', linestyle='--', label=f'Lag Y at 1/e threshold ({lag_when_drops_y})')
if lag_when_drops_z is not None:
    plt.axvline(x=lag_when_drops_z, color='purple', linestyle='--', label=f'Lag Z at 1/e threshold ({lag_when_drops_z})')

plt.xlabel('Lag')
plt.ylabel('Autocorrelation')
plt.title('Autocorrelation of Magnetic Field in Different Coordinates')
plt.legend()
plt.show()

# Print the result
if lag_when_drops_x is not None:
    print(f"The lag at which the autocorrelation of X drops below 1/e of its initial value is: {lag_when_drops_x}")
else:
    print("The autocorrelation of X does not drop below 1/e of its initial value within the computed range.")

if lag_when_drops_y is not None:
    print(f"The lag at which the autocorrelation of Y drops below 1/e of its initial value is: {lag_when_drops_y}")
else:
    print("The autocorrelation of Y does not drop below 1/e of its initial value within the computed range.")

if lag_when_drops_z is not None:
    print(f"The lag at which the autocorrelation of Z drops below 1/e of its initial value is: {lag_when_drops_z}")
else:
    print("The autocorrelation of Z does not drop below 1/e of its initial value within the computed range.")
