# ST2 (Game Theory) - EI Genetic Algorithms

![CentraleSupelec Logo](https://www.centralesupelec.fr/sites/all/themes/cs_theme/medias/common/images/intro/logo_nouveau.jpg)

## Introduction
### Presentation
The aim of this Python project is to circularize a plasmid by optimizing the values of a table calculating the plasmid's 3D trajectory.

_"In microbiology and molecular biology, a plasmid is a DNA molecule distinct from chromosomal DNA.

For this purpose, 2 meta-heuristic algorithms are used and compared: 
- Simulated annealing 
- Genetic algorithm.

### Cost function
A well-defined cost function is essential to ensure the convergence of meta-heuristic algorithms. 

It is defined to minimize the distance between the beginning and the end of the chain and to minimize the angle to ensure coherent junction:

![Screenshot of the cost funtion](/pictures/cost_function.png)

The parameters are :
- $dist$ : the distance between node $n-1$ and $0$.
- $dot_1$: the scalar product between $V_{end}$ and $V_{middle}$. 
- $dot_1$: the scalar product between $V_{middle}$ and $V_{begin}$. 


## Initialization

- Create a Python virtual environment
```sh
python -m venv venv
```
- Activate environment
    - Windows
    ```powershell
    .\venv\Scripts\activate
    ```
    - Linux / macos
    ```sh
    source ./venv/Scripts/activate
    ```

-  Install Python libraries
```sh
pip install -r requirements.txt
```

## Finding a solution

- Simulated annealing algorithm
```sh
python 3dna <directory of the file .fasta> recuit
```
Example
```sh
python 3dna ./data/plasmid_8k.fasta recuit
```

- Genetic algorithm
```sh
python 3dna <directory of the file .fasta file> genetics <selection strategy>
```
List of selection strategies
```
elitisme (défaut)
roulette
rang
tournoi
```

Example
```sh
python 3dna ./data/plasmid_8k.fasta genetique tournoi
```

## Returned value

At the end of an algorithm's execution, several values are printed:
- The 3D rotation table in `json` format
- The difference between the values of *Twist* and *Wedge* and their maximum threshold for each pair of nucleotides (the value of *Direction* is constant)
- The distance between the start and end dinucleotide centers.
- Noting
    - $p_i$ the position of the center of the ith dinucleotide
    - $n$ the number of nucleotides in the sequence
    - $v_{end} = p_{n - 1} - p_{n - 2}$$
    - $v_{middle} = p_0 - p_{n - 1}$$
    - $v_{begin} = p_1 - p_0$

    The cosine similarity between the vectors $v_{end}$ and $v_{middle}$, and $v_{middle}$ and $v_{begin}$.

## Algorithm configuration
The internal values used for the algorithm can be modified in the `3dna/__main__.py` file.
- Simulated annealing algorithm
``` python
recuit = Recuit(
    seq=seq,
    k_max=1000,
    e_min=0.1,
    temp_init=300,
    refroidissement=0.99,
    dist_min=0.,
    relier=1
)
```

|argument|type|description|
|-|-|-|
|seq|str|The nucleotide sequence|
|k_max|int|Maximum number of iterations|
|e_min|float|Energy threshold|
|temp_init|float|Initial temperature|
|refroidissement|float|cooling coefficient|
|dist_min|float|The distance threshold|
|Relier|int|The number of nucleotides linked between the end and the beginning of the sequence|

- Genetic algorithm
```python
genetique = Genetique(
    seq=seq,
    methode_utilisee=args.methode_utilisee,
    nbr_generation_max=150,
    N=50,
    probabilite_mutation_initiale=0.05,
    probabilite_mutation_finale=0.05,
    relier = 1
)
```

|argument|type|description|
|-|-|-|
|seq|str|The nucleotide sequence|
|methode_utilisee|str|The name of the selection strategy|
|nbr_generation_max|int|Maximum number of generations|
|N|int|Population size|
|probabilite_mutation_initiale|float|The probability of initial mutation|
|probabilite_mutation_finale|float|The probability of final mutation|
|Relier|int|The number of nucleotides linked between the end and the beginning of the sequence|

*Note: The mutation probability evolves linearly from the initial value to the final value*.

## Test

A test has been implemented using the `pytest` library.

It can be run with

```
cd 3dna
pytest
```
A coverage report in `html` format can be generated with
```
python -m pytest --cov=. --cov-report html
```

## Statistics

The algorithms can be tested with several hundred iterations to obtain a distribution of the distance between the center of the first and last dinucleotide, and of the minimum cosine similarity.

```bash
python 3dna/stat.py
python 3dna/plot.py
```