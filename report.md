# Report

Xiao Jinsong

## Multi-Energy System

- Supply: wind turbine, PV and diesel generator
- Storage: battery storage system
- Load

### Wind Turbine

Using Weibull distribution to simulate the wind speed. Set $k=2.2, \lambda=8$.
$$
f(x;\lambda,k)=\begin{cases}\frac{k}{\lambda}\left(\frac{x}{\lambda}\right)^{k-1}e^{-(\frac{x}{\lambda})^k}&x\geq0\\ 0&x<0\end{cases}
$$
The relation between WT output and wind speed can be expressed by following function
$$
P_{WT}(v)=
\begin{cases}
0&0\leq v\leq v_{\mathrm{in}}\\ 
P_{N}\frac{v^3-v_{\mathrm{in}}^3}{v_{r}^3-v_{\mathrm{in}}^3} &v_{\mathrm{in}}<v\leq v_{r}\\ 
P_{N}&v_{r}<v\leq v_{\mathrm{out}}\\ 
0&v_{\mathrm{out}}\leq v\\ \end{cases}
$$
$P_{WT}$ and $P_N$: the actual output power and rated output power of a single WT

$v_{\mathrm{in}}$ and $v_{\mathrm{out}}$: the cut-in wind speed and the cut-out wind speed

$v_r$ and $v$: the rated wind speed and the actual speed

**case**

set seed(4)

wind speed: $k=2.2$, $\lambda=8$

<img src="report.assets/image-20230626194141419.png" alt="image-20230626194141419" style="zoom:80%;" />

<img src="figures/wind_power.png" />

### Photovoltaic

$$
P_V=P_{\mathrm{STC}}G_C\frac{1+k(T_C-T_{\mathrm{STC}})}{G_{\mathrm{STC}}}
$$

$P_V$: the actual output power of the PV

$P_{STC}$: the rated output power of the PV

$G_C$: the irradiation of the PV

$T_C$: the surface temperature of the PV

$G_{STC}$ and $T_{STC}$: the rated irradiation and temperature at the rated power

**case**

<img src="report.assets/image-20230626204046812.png" alt="image-20230626204046812" style="zoom:80%;" />

$G_{STC} = 1$ and $T_{STC} = 273+25\degree C$

$k=0.0035$

solar radiation and solar temperature

<img src="figures/PV_arg.png" />

Calculate the solar power: The 'unit in theory' is the unit PV with rated power = 7.3 kW, the actual power is generated from a PV array from Las Vegas(PVDAQ dataset). 

<img src="figures/PV_power.png" />

### Storage

- Charge

remaining capacity of the BESS
$$
E_t = (1-\delta) E_{t-1} + \eta_{charge}{P_{t}^{charge}\Delta t}
$$
$\delta$: the self-leakage rate per delta t of the battery

$\eta_{charge}$: the charging efficiency of the battery during period t

- Discharge

$$
E_t = (1-\delta) E_{t-1} - \frac{P_{t}^{dis} \Delta t}{\eta_{discharge}} \\
$$

SOC
$$
SOC_t = \frac{E_t}{N_{bat}E_{bat,rated}}
$$
Constraints
$$
P_t^{charge} \le N_{bat}P_{rated}^{charge}\theta_{charge}\\
SOC_{\min} \le SOC_t  \le SOC_{\max} \\
P_t^{dis} \le \frac{N_{bat} P_{rated}^{dis}}{\theta_{dis}}
$$
**case**

<img src="report.assets/image-20230627111400625.png" alt="image-20230627111400625" style="zoom:80%;" />

### Diesel Generator

The diesel generator fuel consumption during period t:
$$
Q_t=a\times P_t^{\text{diesel}}+b\times P_{\text{diesel,rated}}
$$
$P_t^{\text{diesel}}$: the diesel generator actual output during period t;

$P_{\text{diesel,rated}}$: the diesel generator rated power

**case**

<img src="report.assets/image-20230627093641389.png" alt="image-20230627093641389" style="zoom:80%;" />

### Load

**case**

<img src="figures/all_load.png" />

### Energy management strategy

- Set: $NWT = 3, NPV = 2, Nbat = 10, Ndie = inf$
- Choose: PV and load data at 2013-06-26

Generated power and load power

<img src="figures/PG_PL.png" />

Power difference and SOC of BESS

<img src="figures/dP_SOC.png" />



### Economic model

#### objective function

$$
C_{sum} = C_{rf}(r,L)  C_{net}\\
C_{npc} = C_{init} + C_{rep} + C_{oper} + C_{main}\\

C_{rf}(r,L)=\frac{r(1+r)^L}{(1+r)^L-1}
$$

$C_{sum}$: the total annual cost of the microgrid

$C_{rf}$: the capital recovery coefficient(资本回收系数)

- *CRF* 是资本回收系数。
- *r* 是贴现率或折现率（discount rate），表示每年资本成本的利率  the actual interest rate
- *L* 是投资的经济寿命（即回收期）total project life time

$C_{net}$: the total net present cost of the microgrid

$C_{init}$: the initial capital cost

$C_{rep}$: replacement cost

$C_{oper}$: operating cost

$C_{main}$: maintenance cost

**Initial capital cost**
$$
C_{init} = N_{WT}P_{WT} C_{WT,init}+ N_{PV}P_{PV} C_{PV,init}+N_{bat}P_{bat} C_{bat,init}+N_{diesel}P_{diesel} C_{diesel,init}
$$
**Maintenance cost**
$$
C_{main}
$$


**Operation cost**




$$
\min C_{sum}=f(N_{WT},N_{PV},N_{bat},N_{diesel})
$$


#### Constraint

number of modules
$$
\begin{gathered}
0\leq N_{WT} \leq N_{WT,\text{max}} \\
0\leq N_{PV} \leq N_{{PV},\max} \\
0\leq N_{bat} \leq N_{\text{bat},\max} \\
0\leq N_{diesel} \leq N_{\text{diesel},\max} 
\end{gathered}
$$
SOC constraint

power constraint

Diesel generator output constraint

power balance
$$
P_{WT}+P_{PV} + P_{bat,dis} + P_{die} = P_{load} + P_{bat,ch} 
$$


## Genetic algorithm(GA)

- Goal: present an optimal configuration for renewable generating systems in residence.
- Purpose: minimize the objective function of GA, achieve an inexpensive and clean electric power system.
- Objective function: the total cost(initial cost, operation cost, maintenance cost ...)

- Advantage: GA overcomes the constraints of derivability and continuity, has good implicit parallelism and global search capability, and uses probabilistic optimization to automatically acquire and optimize the search space, constantly adjusting the search direction of individuals, a process that does not require specific rules.

$$
\min C_{sum}=f(N_{WT},N_{PV},N_{bat},N_{diesel})
$$



## Case Study



| Module       | Unit power/kW(h) | initial cost($/kW) | maintenance cost($/kW/year, \$/h) | O&M cost($/kWh) | life time |
| ------------ | ---------------- | ------------------ | --------------------------------- | --------------- | --------- |
| Wind turbine | 5                | 2000               | 33                                | 0.01            | 24 y      |
| PV           | 7.3              | 3400               | 16                                | 0.008           | 24 y      |
| BESS         | 10               | 280                | 10                                | 0.01            | 12 y      |
| Diesel       | 4                | 1000               | 0.05                              | 0.08            | 24000 h   |





## Schedule

06-26: Read the paper and complete a preliminary understanding of the project.

06-27: Performed mathematical modeling of each module in a multi-energy system, and simulated and collected data on wind power, photovoltaic power, and load, BESS. 

06-28: Completion of the opening report and modeling of the economic model. 



## Reference

[1] 
