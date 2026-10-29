# LSTM-bandwidth-demand-predictor

A time-series forecasting pipeline designed to predict the next 5 seconds of mobile LTE throughput. This model enables network systems to make proactive, real-time decisions in adaptive video streaming, rate control, and capacity planning.

Dataset & Scope
• Source: Public NYU-METS mobile bandwidth traces (Mei et al., PAM 2019 / Computer Networks 2020).
• Current Focus: The Q-Train subway trace (Prospect Park to Coney Island) featuring 7,455 high-variance readings.
• Characteristics: Highly volatile network conditions consisting of two primary regimes—a low-regime background (0–20 Mbit/s) punctuated by extreme bursts up to ~1 Gbit/s.

Data Pipeline & Features

To capture both trend and volatility, raw 1-second sampled network CSV files are transformed into multi-feature sliding windows:
1. Log Transformation & Normalization: Applied via log1p and z-score scaling (fitted strictly on training data).
2. Feature Engineering: Each sequence step maps 4 features: the raw throughput level, instant delta change, rolling mean (5s), and rolling standard deviation (5s).
3. Target Setup: The network shifts to a predict-the-change configuration, forcing the model to learn the delta from the last known value rather than absolute throughput.
4. Data Splitting: Chronological 70% / 15% / 15% split to prevent data leakage.

Model Architecture

• Input Block: Sequence window (W=30) × 4 engineered features.
• Core Layers: 2-layer LSTM network (128 hidden units, 0.2 dropout).
• Output Block: Dense layer (128 units, ReLU activation, 0.2 dropout) mapping to a final linear layer outputting 5 future delta steps.
• Loss Function: Optimized using L1 Loss (Mean Absolute Error) to ensure robustness against massive 1 Gbit/s throughput spikes.
