% McCabe-Thiele Method for Binary Distillation
clear; clc;

fprintf('--- McCabe-Thiele Plotter ---\n');
xf = input('Enter mole fraction of feed (xf): ');
xd = input('Enter mole fraction of distillate (xd): ');
xw = input('Enter mole fraction of bottoms (xw): ');
R = input('Enter Reflux Ratio (R): ');
q = input('Enter Feed Condition (q) [1 for sat. liquid, 0 for sat. vapor]:');
alpha = input('Enter Relative Volatility (alpha): ');

% Equilibrium Curve
x = 0:0.01:1;
y_eq = (alpha .* x) ./ (1 + (alpha - 1) .* x); 

% Rectifying Line
slope_rect = R / (R + 1);
inter_rect = xd / (R + 1);

%  q-line
if q == 1
    xi = xf;
    yi = slope_rect * xi + inter_rect;
else
    xi = (inter_rect + xf/(q-1)) / (q/(q-1) - slope_rect);
    yi = slope_rect * xi + inter_rect;
end

% Stripping Line
slope_strip = (yi - xw) / (xi - xw);
inter_strip = xw - slope_strip * xw;

% Stages
curr_x = xd;
curr_y = xd;
stages_x = [xd];
stages_y = [xd];
num_stages = 0;

while curr_x > xw
    num_stages = num_stages + 1;

    % Moving  horizontally to Equilibrium Curve
    curr_x = curr_y / (alpha - curr_y * (alpha - 1));
    stages_x = [stages_x, curr_x];
    stages_y = [stages_y, curr_y];
    
    % Moving vertically to Operating Line
    if curr_x > xi
        curr_y = slope_rect * curr_x + inter_rect;
    else
        curr_y = slope_strip * curr_x + inter_strip;
    end
    stages_x = [stages_x, curr_x];
    stages_y = [stages_y, curr_y];
end

% Plot
figure('Color', 'b'); 
hold on; grid on;
plot(x, x, 'y--', 'LineWidth', 1); % 45-degree line
plot(x, y_eq, 'b', 'LineWidth', 2); % Equilibrium curve
plot([xd, xi], [xd, yi], 'r', 'LineWidth', 1.5); % Rectifying line
plot([xw, xi], [xw, yi], 'g', 'LineWidth', 1.5); % Stripping line
plot([xf, xi], [xf, yi], 'm--', 'LineWidth', 1.5); % q-line
plot(stages_x, stages_y, 'y', 'LineWidth', 1.2); % Stages

xlabel('x (Liquid mole fraction)'); 
ylabel('y (Vapor mole fraction)');
title(['McCabe-Thiele Plot (Theoretical Stages:', num2str(num_stages),')']);
legend('45 Line', 'Equilibrium', 'Rectifying', 'Stripping', 'q-line', ...
    'Stages', 'Location', 'NW');