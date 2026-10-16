# Filtrage de Kalman d'un oscillateur : restitution du signal et détection d'EMI

Filtre de Kalman à état 2D appliqué à un oscillateur (circuit RLC) perturbé par des impulsions.


## Contexte

Le fonctionnement du filtre de kalman est soumis à des conditions de linéarité et gaussieneté du bruit, pour tester les limites du filtre, il m'a fallu une méthode avec laquelle je vérifie la pertinence du signal résultant après filtrage, l'idée alor était de procéder par rétroaction, on connait un signal d'origine, on le perturbe par un microcontrolleur qui envoie des pulsions de rapport cycle pouvant être variable, on obtient ainsi un signal bruité qui ne respecte pas les conditions du filtrage (bruit non gaussier), et on compare me signal après filtrage à celui qui n'a subi aucun bruit.

Le montage utilisé est celui de montageElectrocinétique.png

## Modèle

L'oscillateur est décrit par un état à deux composantes $x_k = (x_{1,k},\, x_{2,k})^\top$ [tension aux bornes de la bobine et courant dans le circuit].

$$x_k = F\,x_{k-1} + w_k, \qquad z_k = H\,x_k + v_k$$

avec $w_k \sim \mathcal{N}(0, Q)$ et $v_k \sim \mathcal{N}(0, R)$.

- $F$ : matrice de transition, obtenue en discrétisant l'équation de l'oscillateur au pas $\Delta t$ = 1e-5.
- $H$ : matrice d'observation = [1  0].
- $Q$, $R$ : covariances des bruits de modèle et de mesure, choisies ainsi : Covariance du bruit de modèle par Tuning. Covariance du bruit de mesure par calcul de la moyenne du bruit de fond quand la tension d'entrée est nulle.

## Algorithme

**Prédiction**

$$\hat{x}_{k|k-1} = F\,\hat{x}_{k-1|k-1}, \qquad P_{k|k-1} = F\,P_{k-1|k-1}F^\top + Q$$

**Mise à jour**

$$\nu_k = z_k - H\,\hat{x}_{k|k-1}, \qquad S_k = H\,P_{k|k-1}H^\top + R$$

$$K_k = P_{k|k-1}H^\top S_k^{-1}$$

$$\hat{x}_{k|k} = \hat{x}_{k|k-1} + K_k\,\nu_k, \qquad P_{k|k} = (I - K_k H)\,P_{k|k-1}$$

Le gain $K_k$ converge vers une valeur stationnaire : 0.0018 .


## Résultats

![Signal mesuré, signal filtré et signal d'origine](figures/resultat_filtrage.png)

| Grandeur | Mesure brute | Après filtrage |
|---|---|---|
| Erreur RMS | [À COMPLÉTER] | [À COMPLÉTER] |

![Statistique du χ² et seuil de détection](figures/detection_chi2.png)

## Utilisation

```bash
pip install -r requirements.txt
python kalman.py
```

[À COMPLÉTER : paramètres modifiables, fichier de données utilisé.]

## Structure du dépôt
