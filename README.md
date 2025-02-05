# Statistical-reproducibility-Python
Measuring Statistical reproducibility using Jupyter (Python)


import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
import os

np.random.seed(123)
# Define standard deviations

# Normal, m=10, perfect; sdrss = 1.3;  sdmrss = 1;    sderss = 2;    sdprss = 1.4
# Normal, m=10, 0.90;    sdrss = 1.36; sdmrss = 1.07; sderss = 2.10; sdprss = 1.49
# Normal, m=10, 0.50;    sdrss = 1.40; sdmrss = 1.09; sderss = 2.13; sdprss = 1.52 
# Normal, m=6, perfect   sdrss = 1.59; sdmrss = 1.29; sderss = 2.32; sdprss = 1.73
# Normal, m=6, 0.90      sdrss = 1.55; sdmrss = 1.25; sderss = 2.29; sdprss = 1.69
# Normal, m=6, 0.50      sdrss = 1.60; sdmrss = 1.32; sderss = 2.40; sdprss = 1.799

# Exp, m=10, perfect;    sdrss = 0.30;  sdmrss = 0.22;    sderss = 0.47;    sdprss = 0.33
# Exp, m=10, 0.90;       sdrss = 0.33;  sdmrss = 0.25;    sderss = 0.50;    sdprss = 0.35
# Exp, m=10, 0.50;       sdrss = 0.35;  sdmrss = 0.27;    sderss = 0.53;    sdprss = 0.37
# Exp, m=6, perfect      sdrss = 0.40;  sdmrss = 0.32;    sderss = 0.57;    sdprss = 0.43
# Exp, m=6, 0.90         sdrss = 0.43;  sdmrss = 0.35;    sderss = 0.60;    sdprss = 0.47
# Exp, m=6, 0.50         sdrss = 0.45;  sdmrss = 0.37;    sderss = 0.63;    sdprss = 0.52

# RD, n=10, perfect;     sdrss = 5;     sdmrss = 3.5;     sderss = 8.1;     sdprss = 5.4
# RD, n=10, Imperfect    sdrss = 5.6;   sdmrss = 4.05;    sderss = 9.2;     sdprss = 6
# RD, n=6, perfect;      sdrss = 6.7;   sdmrss = 4.5;     sderss = 10;      sdprss = 7.13
# RD, n=6, Imperfect     
sdrss = 7.1;   sdmrss = 4.6;     sderss = 11;      sdprss = 7.26







# Initialize arrays for storing results
AAD_rss = []; AAD_mrss = []; AAD_erss = []; AAD_prss = []
MSD_rss = []; MSD_mrss = []; MSD_erss = []; MSD_prss = []

# Simulation parameters
sim = 1000; M = 100

# Simulations
for _ in range(sim):
    Ybrss = np.random.normal(M, sdrss)
    Ybmrss = np.random.normal(M, sdmrss)
    Yberss = np.random.normal(M, sderss)
    Ybprss = np.random.normal(M, sdprss)

    Ybrss_rt = Ybrss + np.random.normal(0, sdrss)
    Ybmrss_rt = Ybmrss + np.random.normal(0, sdmrss)
    Yberss_rt = Yberss + np.random.normal(0, sderss)
    Ybprss_rt = Ybprss + np.random.normal(0, sdprss)

    AAD_rss.append(np.mean(np.abs(Ybrss - Ybrss_rt)))
    AAD_mrss.append(np.mean(np.abs(Ybmrss - Ybmrss_rt)))
    AAD_erss.append(np.mean(np.abs(Yberss - Yberss_rt)))
    AAD_prss.append(np.mean(np.abs(Ybprss - Ybprss_rt)))

    MSD_rss.append(np.mean((Ybrss - Ybrss_rt) ** 2))
    MSD_mrss.append(np.mean((Ybmrss - Ybmrss_rt) ** 2))
    MSD_erss.append(np.mean((Yberss - Yberss_rt) ** 2))
    MSD_prss.append(np.mean((Ybprss - Ybprss_rt) ** 2))

# Define color palette
colors = { 'RSS': '#1f77b4', 'MRSS': '#2ca02c', 'ERSS': '#d62728', 'PRSS': '#ba55d3' }

# CDF for AAD
val_aad = pd.DataFrame({ 'RSS': AAD_rss, 'MRSS': AAD_mrss, 'ERSS': AAD_erss, 'PRSS': AAD_prss })
mx_aad = np.max([val_aad[col].max() for col in val_aad.columns])

plt.figure(figsize=(8, 6))
for col in val_aad.columns:
    sns.ecdfplot(val_aad[col], label=col, linewidth=2, color=colors[col])

plt.xlim(0, mx_aad)
plt.ylim(0, 1)
plt.xlabel(r'$\varepsilon$', fontsize=14)  # Changed label here
plt.ylabel(r'$R{P_1}(\varepsilon)$', fontsize=14)  # Changed label here
plt.title(r'Imperfect ranking, $n=6$', fontsize=16)
plt.legend(loc='lower right', fontsize=12)
plt.box(on=True)  # Keep borders
aad_plot_path = r"C:\Users\Syed Abdul Rehman\OneDrive\Desktop\images\AAD.jpg"
plt.savefig(aad_plot_path, format='jpg')  # Save as JPG
plt.close()

# CDF for MSD
val_msd = pd.DataFrame({
    'RSS': MSD_rss,
    'MRSS': MSD_mrss,
    'ERSS': MSD_erss,
    'PRSS': MSD_prss
})
mx_msd = np.max([val_msd[col].max() for col in val_msd.columns])

plt.figure(figsize=(8, 6))
for col in val_msd.columns:
    sns.ecdfplot(val_msd[col], label=col, linewidth=2, color=colors[col])

plt.xlim(0, mx_msd)
plt.ylim(0, 1)
plt.xlabel(r'$\varepsilon$', fontsize=14)  # Changed label here
plt.ylabel(r'$R{P_2}(\varepsilon)$', fontsize=14)  # Changed label here
plt.title(r'Imperfect ranking, $n=6$', fontsize=16)
plt.legend(loc='lower right', fontsize=12)
plt.box(on=True)  # Keep borders
msd_plot_path = r"C:\Users\Syed Abdul Rehman\OneDrive\Desktop\images\MSD.jpg"
plt.savefig(msd_plot_path, format='jpg')  # Save as JPG
plt.close()

print(f"Plots saved as JPG to {aad_plot_path} and {msd_plot_path}")
