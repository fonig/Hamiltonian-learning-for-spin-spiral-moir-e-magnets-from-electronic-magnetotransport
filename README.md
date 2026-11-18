# Hamiltonian-learning-for-spin-spiral-moir-e-magnets-from-electronic-magnetotransport
This repository contains data and scripts for generating the results of the manuscript "Hamiltonian learning for spin-spiral moiré magnets from electronic magnetotransport ".

Authors: Fedor Nigmatulin, Greta Lupi, Jose L. Lado, Zhipei Sun, [arXiv:2604.02959]

This record contains the conductance datasets, the data shown in the figures, and two Jupyter notebooks that train the neural network which predicts the q-vector from conductance maps and evaluate the fidelities of the predictions.

## Contents

```
Conductance_datasets/
    Full_Datasets/
        q_vectors_J(0.2).txt                 q-vectors of the J = 0.2 dataset
        q_vectors_J(1.0).txt                 q-vectors of the J = 1.0 dataset
        conductance_dataset_J(0.2)/          9981 conductance maps, J = 0.2
        conductance_dataset_J(1.0)/          9996 conductance maps, J = 1.0
    Additional_testing_datasets_for_Fig_4E/
        testing_q_vectors.csv                805 q-vectors used for testing with different J
        Testing_conductance_dataset_J(0.4)/  conductance maps, J = 0.4
        Testing_conductance_dataset_J(0.6)/  conductance maps, J = 0.6
        Testing_conductance_dataset_J(0.8)/  conductance maps, J = 0.8
Training_script.ipynb                               model, one training, fidelities, prediction plots
Fidelity_vs_J_test.ipynb                               fidelities as a function of J_test (Fig. 4E)
```

## Data format

- **Conductance maps.** One CSV file per q-vector, named `q1_<q1>_q2_<q2>.csv` with both components written with 4 decimals. Each file has the columns `Magnetic_flux`, `Chemical_potential`, `Conductance` on a 51 x 51 grid (magnetic flux from 0 to 1, chemical potential from -4.5 to 4.5).
- **q-vectors.** Two columns, the components (q1, q2) of the q-vector. Both components take values between 0 and 0.5.
- **Testing q-vectors.** `testing_q_vectors.csv` lists the 805 q-vectors that belong to the test split of both the J = 0.2 and the J = 1.0 datasets (split with `test_size=0.2`, `random_state=42`, as in `train_model`). They are therefore not used in the training or validation of either model.
## Requirements

Python 3 with numpy, pandas, scikit-learn, tensorflow (keras) and matplotlib. The results were obtained with:

| Package | Version |
|---|---|
| Python | 3.11.4 |
| numpy | 1.24.4 |
| pandas | 1.5.3 |
| scikit-learn | 1.3.0 |
| tensorflow | 2.19.0 |
| matplotlib | 3.7.1 |

The conductance datasets were generated with pyqula 0.0.84.

## How to run

Start Jupyter in this folder (the one that contains `Conductance_datasets/`) and run the notebooks from top to bottom. All paths in the notebooks are relative to this folder.


### Training_script.ipynb

Trains one model on one dataset and tests it on the held-out test split of the same dataset.

- **Settings** (first code cell after the imports): `J_VALUE` ("1.0" or "0.2") selects the dataset, `N_COMPONENTS` is the number of PCA components, `ETA_TRAIN` and `ETA_TEST` are the training and test noise levels.
- **Output:** cumulative variance of the PCA components, training history, the fidelities F_|q| and F_theta, and the plots of predicted versus true |q| and theta.

Results for other noise levels or for J = 0.2 are obtained by changing the settings and running the notebook again.

### Fidelity_vs_J_test.ipynb

Trains models at J_train = 0.2 and J_train = 1.0 and tests them on conductance maps computed with J_test = 0.2, 0.4, 0.6, 0.8 and 1.0.

- **Settings:** `J_TRAIN_VALUES`, `J_TEST_VALUES`, `N_TRAININGS` (independent trainings per J_train), `ETA_TEST_J` (test noise level), `N_AVG` (test-noise realizations).
- **Output:** `Fidelity_vs_J_test.csv` with the mean fidelities, and a plot of the mean fidelities versus J_test.
- **Settings used for Fig. 4E:** `N_TRAININGS = 60`, `N_AVG = 10`, `ETA_TEST_J = 0.05`. For each trained model the predictions are averaged over the `N_AVG` test-noise realizations and the fidelities are computed from the averaged prediction; the fidelities are then averaged over the trainings.
