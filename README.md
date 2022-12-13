# pipeworks
* Repository for the `pipeworks` `Python` package. 
* Contains a collection of classes and functions for computing pressurized and open channel hydraulics for outlet works.
* Current development branch in `/dev`. Release versions are archived in `/packages`.


## Installation
Install from [PyPI](https://pypi.org/project/pipeworks/) using `pip`:
```
pip install pipeworks
```

For a particular version, use:
```
pip install pipeworks=0.0.2
```


Alternatively, install from [Anaconda](https://anaconda.org/kckuei/pipeworks) using `conda`:
```
conda install -c kckuei pipeworks
```

Verify the installation:
```
python3
import pipeworks as pw
help(pw)
```

## Examples

Example - Weir Rating Curve
```python
import pipeworks as pw
import matplotlib.pyplot as plt

length = 45
height = 3.9
dH = 3.5

weir = pw.WeirFlow(length, height)
weir.calc_rating(dH)
weir.save_rating()
plt.plot(weir.rating.Q, weir.rating.height)
plt.xlabel('Discharge [cfs]')
plt.ylabel('Height [ft]')
plt.show()
```

Example - Drop Inlet Rating Curve
```python
import pipeworks as pw
import matplotlib.pyplot as plt

diam = 72/12
dH = 9

drop = pw.ShaftFlow(diam, 0)
drop.calc_rating(dH)
drop.save_rating()
plt.plot(drop.rating.Q, drop.rating.height)
plt.xlabel('Discharge [cfs]')
plt.ylabel('Height [ft]')
plt.show()

```

Example - Pressurized Section
```python
import pipeworks as pw
import matplotlib.pyplot as plt

length, diam = 900, 72/12
C, K = 140, 5
z1, z2 = 10, 0

sec = pw.Section(length, diam)
sec.set_pressurized_params(C, K, z1, z2)
sec.calc_pressurized(10)

fig, ax = plt.subplots()
__, ax = sec.plot_pressurized('Q', 'depths', ax = ax, c='tab:blue')
plt.xlabel('Discharge [cfs]')
plt.ylabel('Headwater [ft]')
plt.show()  

# Check validity of results (sum of energy terms should be near zero)
sec.validate_rating_press(plot=True)
```
