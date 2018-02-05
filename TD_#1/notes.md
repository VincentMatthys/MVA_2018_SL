# Notes for TD1

Sources:
  + http://haythamfayek.com/2016/04/21/speech-processing-for-machine-learning.html

## Mel-filterbank

## MFCC
_Mel-frequency Cesptral Coefficients_

## Logistic regression

+ $53.8\%$
```
mfcc = spectral.Spectral(nfilt=20,
                    ncep=8,
                    do_dct=True,
                    lowerf=20,
                    upperf=8000,
                    alpha=0.6,
                    fs=framerate,
                    frate=100,
                    wlen=0.035,
                    nfft=512,
                    compression='log',
                    do_deltas=True,
                    do_deltasdeltas=False)
```
+ $57.6\%$
```
mfcc = spectral.Spectral(nfilt=40,
                    ncep=13,
                    do_dct=True,
                    lowerf=133,
                    upperf=6856,
                    alpha=0.97,
                    fs=framerate,
                    frate=100,
                    wlen=0.1,
                    nfft=512,
                    compression='log',
                    do_deltas=True,
                    do_deltasdeltas=False)
```
```
logreg = sklearn.linear_model.LogisticRegression(verbose=1,
                                                 tol=1e-5,
                                                 C = 1,
                                                 random_state=777,
                                                 solver = "saga",
                                                 multi_class = "multinomial",
                                                 n_jobs = 6)
```



## MLP

+ $80.2\%$
```
neural_net = MLPClassifier(hidden_layer_sizes=(200, 200),
                           alpha = 1e-3,
                           validation_fraction = 0.2,
#                            early_stopping = True,
                           verbose = True,
                           random_state = 777,
#                            solver = "lbfgs",
                           learning_rate='adaptive',
                           tol = 1e-5,
                           learning_rate_init=0.0005,
                           shuffle = True,
                           max_iter = 400)
```
no change with (300, 300)
lower with:
  + (230, 230)
  + (180, 180)
  + (150, 150)
  + (120, 120)
  + (100, 100)
