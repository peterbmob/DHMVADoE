
# Project in the course Design of Experiemnts, Data Handling and Statistical Analysis for Material Scientists

matminer is a Python library for data mining the properties of materials.

Matminer contains routines for 40+ datasets (described [here](https://hackingmaterials.lbl.gov/matminer/dataset_summary.html))

The datasets available by loading the following modules:

- load_boltztrap_mp()
- load_brgoch_superhard_training()
- load_castelli_perovskites()
- load_citrine_thermal_conductivity()
- load_dielectric_constant()
- load_double_perovskites_gap()
- load_double_perovskites_gap_lumo()
- load_elastic_tensor()
- load_expt_formation_enthalpy()
- load_expt_gap()
- load_flla()
- load_glass_binary()
- load_glass_ternary_hipt()
- load_glass_ternary_landolt()
- load_heusler_magnetic()
- load_jarvis_dft_2d()
- load_jarvis_dft_3d()
- load_jarvis_ml_dft_training()
- load_m2ax()
- load_mp()
- load_phonon_dielectric_mp()
- load_piezoelectric_tensor()
- load_steel_strength()
- load_wolverton_oxides()



## Task:
1. Choose a suitable material property.
2. Investigate what features are important to describe it. 
3. Build a model with as few variables as possible that describe the property you selected.
4. Predict/optimize the property at hand... here you must have an "imaginary" application for the property you have choosen. Try to figure out a case where you want a certain value for the proerty at hand.


In the report, I want to see how you set up the workflow (from data aqcuisition to sharing the results) for solving the problem. Try to write an introduction from a materials science point of view.


# Hints: 

## Available datasets: 

```
from matminer.datasets import get_available_datasets

get_available_datasets()
```

## Info about data set: 


```
from matminer.datasets import get_all_dataset_info

print(get_all_dataset_info("expt_gap"))
```

## load the data set:

```
from matminer.datasets.convenience_loaders import load_elastic_tensor
df = load_elastic_tensor()  # loads dataset in a pandas DataFrame object

```
## Watch the data



```
from ydata_profiling import ProfileReport

profile = ProfileReport(df, title="Profiling Report")

profile.to_notebook_iframe()
```



## Watch columns:



```
df.head()
```

## Drop unwanted columns:


```
unwanted_columns = ["volume", "nsites", "compliance_tensor", "elastic_tensor",
                    "elastic_tensor_original", "K_Voigt", "G_Voigt", "K_Reuss", "G_Reuss"]
df = df.drop(unwanted_columns, axis=1)
```

## Filtering 

```
mask = df["gap expt"] > 0
nonmetal_df = df[mask]
nonmetal_df
```


## Featurization

Matminer has its own featurizer described [here](https://hackingmaterials.lbl.gov/matminer/featurizer_summary.html). More features can be obtained using the [CBFV package](https://github.com/Kaaiian/CBFV).


Here is an example using the column formula in the dataset 'expt_gap' to find the composition: 
```
from pymatgen.core import Composition
from matminer.featurizers.composition.element import ElementFraction

ef = ElementFraction()

ff=[]
for formel in df['formula']:
  ff.append(Composition(formel))

df['composition']=ff
            
df

```

which can thereafter be used with the CBFV package. 

```
from CBFV.composition import generate_features
X_train, y_train, formulae_train, skipped_train = generate_features(df_train, elem_prop='oliynyk', drop_duplicates=False, extend_features=True, sum_feat=True)

```
From here, we dive in to our multiregression/multivariate analysis tasks to build our model. 
