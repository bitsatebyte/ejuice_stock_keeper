# E-juice Stock keeper

### **Description**

This is an e-juice stock keeping app. It alerts the user whenever he's running out of supplies let's say to create at least one recipe from his saved cookbooks.

Initial configuration options for the user are - 

| Option | Datatype | Desc |
| ------ | -------- | ---- |
| Standard Recipe Volume | Number | Volume in _mL_ to create one vial |
| Minimum Recipe Creation Ability | Number | How many vials of e-juice should a user be able to make before he/she is alerted |
| VG Ratio | Number | in units (vg+pg should not exceed 100) |
| PG Ratio | Number | in units (vg+pg should not exceed 100) |
| Nicotine Concentration | Number | in _mg/mL_ (Pure nicotine has 990 mg/mL Strength) |


#### EXAMPLE

##### ___config options___

```javascript
options = {
    "standardRecipeVolume" : 100,
    "minimumRecipes" : 3,
    "vgRatio" : 70,
    "pgRatio": 30,
    "nicotineStrength": 5
}
```

##### ___stock options___

```javascript
liquidStock = {
    "vg": 500,
    "pg": 500,
    "nicotine": {
        "strength": 100,
        "volume": 120,
        "isVg": false,
        "type": "PG"
    },
    "flavors": [
        {
            "name:" "TFA Strawberry (Ripe)",
            "isVg": true,
            "type": "VG",
            "volume": 10,
        },
        {
            "name": "Capella Bavarian Cream",
            "isVg": false,
            "type": "PG",
            "volume": 10,
        }
    ],
}
```

##### ___mix options___

```javascript
mixOptions = {
    "batch": {
        "volume": 60,
    },
    "nicotineProperties": {
        "strengthUnit": "Weight",
        "strength": 100,
        "isVg": false,
        "vg": 0,
        "pg": 100,
    },
    "flavours": [
        {
            "name": "TFA Strawberry (Ripe)"
            "isVg": false,
            "percentage": 3,
        },
    ],
    "targetJuiceProperties": {
        "nicotineStrength": 3,
        "vg": 70,
        "pg": 30,
    },
}
```

Lets do the math: 

> 100 _mL_ * 3 = 300 _mL_  
> **VG + PG + Flavour(s) + Nicotine = 180 _mL_**

| Item | Density (_g/mL_) |
| ---- | -------------- |
| Pure Nicotine | 1.0 |
| Nicotine _(PG)_| 1.03 |
| Nicotine _(VG)_| 1.24 |
| VG | 1.26 |
| PG | 1.03 |
| Flavor _(PG)_ | 1.05 |
| Flavor _(VG)_ | 1.16 |

> Let's calculate for each vial (100 _mL_)

### Juice Properties
- VG - 70%
- PG - 30%
- Nicotine Strength - 5 _mg/mL_
- Flavor_1(PG) - 3%
- Flavor_2(VG) - 2%



```javascript
recipe = [
    {
        "element": "Nicotine base (PG)",
        "volume": 5.00,
        "weight": 5.17,
    },
    {
        "element": "Capella Bavarian Cream",
        "volume": 3.00,
        "weight": 3.15,
    },
    {
        "element": "TFA Strawberry (Ripe)",
        "volume": 2.00,
        "weight": 2.10,
    },
    {
        "element": "Vegetable Glycerin",
        "volume": 67.65,
        "weight": 85.33,
    },
    {
        "element": "Propylene Glycol",
        "volume": 22.35,
        "weight": 23.18,
    },
]
```

### __Target recipe volume calculation (Formulae)__

##### Variables
1. PG Ratio = p
2. VG Ratio = v
3. Nicotine Strength = s
4. PG Flavor Percent = pF
5. VG Flavor Percent = vF
6. PG Ratio || VG Ratio ( p || v ) = i
7. PG Flavor Percent || VG Flavor Percent ( pF || vF ) = j

##### Expressions for VG/PG Volume (in _mL_)
- _expr_1 = ( i * n )/1000_
- _expr_2 = n - ( n/10 )_
- _expr_3 = j = j_1 + j_2 + j_3 + ... + j_n_
- _expr_5 = i - ( expr_1 + expr_2 + expr_3 )_

##### Expressions for Nicotine & Flavor (in _mL_)
- _expr_5 = n_
- _expr_6 = f_

##### Rules
- _p + v = 100_
- _j = j_1 + j_2 + j_3 + ... + j_n where [j_1 , j_2 , j_3 , ... j_n] are different flavors_
- if there are no flavors in __VG__, then _j = 0_ for _expr_3_ when _i is p_
- if there are no flavors in __PG__, then _j = 0_ for _expr_3_ when _i is v_
- if there are both __VG__ & __PG__ flavors, then they both add up to their individual expressions to then calculate the final resulting volume for their respective bases.
- if nicotine is in __VG__, then _expr_2 = 0_ for _expr_3_ when _i = p_
- if nicotine is in __PG__, then _expr_2 = 0_ for _expr_3_ when _i = v_

## Stock Calculation Formulae

_note: everytime a recipe is created, stock will go down_

```javascript
newLiquidStock = {
    "vg": 432.35,
    "pg": 477.65,
    "nicotine": {
        "strength": 100,
        "volume": 115,
    },
    "flavors": [
        {
            "name:" "TFA Strawberry (Ripe)",
            "isVg": true,
            "type": "VG",
            "volume": 7,
        },
        {
            "name": "Capella Bavarian Cream",
            "isVg": false,
            "type": "PG",
            "volume": 10,
        }
    ],
}
```


