# Signal-Analysis
% Signal Analysis Code to use in MatLab
% Signal to Noise Ratio - Script made by LMD for the thesis: AGING VOICES: ACOUSTIC CHARACTERISTICS OF SENESCENCE AND PARKINSON'S DISEASE; 
% Author: Dal'Ava, L. M. Supervisor: Barbosa, P. A.

% Step 1: Select the WAV file
initial_folder = 'G:\Meu Drive\Áudios - Doutorado\Cada Participante'; % Set initial folder
[filename, pathname] = uigetfile(fullfile(initial_folder, '*.wav'), 'Select a WAV file');
if isequal(filename,0)
   disp('No file selected.'); 
   return;
end
file_path = fullfile(pathname, filename);

% Step 2: Load the WAV file
[signal, fs] = audioread(file_path);

% Step 3: Display the signal and allow the user to select the signal and noise intervals
figure;
plot((1:length(signal))/fs, signal);
xlabel('Time (s)');
ylabel('Amplitude');
title('Select the signal interval');

% User selects the signal interval
[x_signal, ~] = ginput(2); % Select two points on the plot for the signal
if length(x_signal) < 2
    disp('Select two points to define the signal interval.');
    return;
end

% Convert the selected points to indices for the signal
start_time_signal = min(x_signal);
end_time_signal = max(x_signal);
start_index_signal = round(start_time_signal * fs);
end_index_signal = round(end_time_signal * fs);

% Check if indices are within valid range for the signal
if start_index_signal < 1
    start_index_signal = 1;
end
if end_index_signal > length(signal)
    end_index_signal = length(signal);
end

% Extract the selected signal interval
signal_interval = signal(start_index_signal:end_index_signal);

% Step 4: Now select the noise interval
title('Select the noise interval');

% User selects the noise interval
[x_noise, ~] = ginput(2); % Select two points on the plot for noise
if length(x_noise) < 2
    disp('Select two points to define the noise interval.');
    return;
end

% Convert the selected points to indices for the noise
start_time_noise = min(x_noise);
end_time_noise = max(x_noise);
start_index_noise = round(start_time_noise * fs);
end_index_noise = round(end_time_noise * fs);

% Check if indices are within valid range for the noise
if start_index_noise < 1
    start_index_noise = 1;
end
if end_index_noise > length(signal)
    end_index_noise = length(signal);
end

% Extract the selected noise interval
noise_interval = signal(start_index_noise:end_index_noise);

% Step 5: Calculate signal amplitude (mean absolute amplitude)
signal_amplitude = mean(abs(signal_interval));

% Step 6: Calculate noise amplitude (mean absolute amplitude)
if isempty(noise_interval)
    disp('The noise interval is empty. Check the selected interval.');
    return;
end
noise_amplitude = mean(abs(noise_interval));

% Step 7: Calculate SNR based on amplitude
if noise_amplitude == 0
    disp('Noise amplitude is zero. SNR calculation cannot be performed.');
    return;
end
SNR = 20 * log10(signal_amplitude / noise_amplitude);

% Step 8: Display the result
disp(['Signal-to-Noise Ratio (SNR) based on amplitude is: ', num2str(SNR), ' dB']);
