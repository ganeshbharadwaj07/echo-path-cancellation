% Echo Path Cancellation Simulation using LMS Adaptive Filter

% Parameters
fs = 8000;                    % Sampling rate (Hz)
duration = 1;                 % Duration of the signal (seconds)
t = (0:1/fs:duration-1/fs)';  % Time vector

% Input signal: sum of two sine waves representing speech-like signal
input_signal = sin(2*pi*10*t) + 0.5*sin(2*pi*100*t);

% Create echo signal by delaying and attenuating the input
echo_delay = 200;             % Echo delay (samples)
echo_attenuation = 0.6;       % Echo attenuation factor
echo_signal = [zeros(echo_delay, 1); input_signal(1:end-echo_delay)] * echo_attenuation;

% Combined received signal with echo
received_signal = input_signal + echo_signal;

% LMS adaptive filter settings
filter_order = 256;            % Number of filter coefficients
mu = 0.01;                    % Step size (learning rate)
weights = zeros(filter_order, 1);  % Initial filter weights (zero)
n_samples = length(received_signal);

% Initialize output vectors
estimated_echo = zeros(n_samples, 1);  
error_signal = zeros(n_samples, 1);

% Adaptive filtering using LMS
for n = filter_order:n_samples
    x = received_signal(n-filter_order+1:n);  % Input vector segment
    x = flipud(x);                             % Flip for convolution
    estimated_echo(n) = weights' * x;          % Echo estimate from filter
    error_signal(n) = received_signal(n) - estimated_echo(n);  % Error (residual echo)
    weights = weights + 2 * mu * error_signal(n) * x;          % LMS weight update
end

% Plot results
subplot(3, 1, 1);
plot(input_signal);
title('Original Signal');
xlabel('Sample Number');
ylabel('Amplitude');

subplot(3, 1, 2);
plot(received_signal);
title('Received Signal (with Echo)');
xlabel('Sample Number');
ylabel('Amplitude');

subplot(3, 1, 3);
plot(error_signal);
title('Error Signal (After Echo Cancellation)');
xlabel('Sample Number');
ylabel('Amplitude');
