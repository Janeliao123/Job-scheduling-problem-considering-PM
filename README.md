# A job scheduling problem considering preventive maintenance

## 1.Introduction

Job scheduling and maintenance planning are two of the most important topics for manufacturing business. Companies that plan the job sequence and maintenance properly can not only save more budget but also spend less time to get the same yield. 

### 1.1 Job scheduling problem

There are many different methods to solve the job scheduling problem, and two of the most common methods are mathematical model and algorithm. Mathematical model solves the job scheduling problem as an optimization problem without considering the maintenance. On the other hand, many people design an algorithm to decide the job sequence by WSPT (Weighted Shortest Processing Time). The WSPT calculates the ratio of weight (importance) of a job divided by the processing time, and the larger the ratio is, the former the job is in the job sequence. 

### 1.2 Maintenance planning problem

For maintenance planning, most types of maintenance fall into two main categories: corrective and preventive maintenance (Mobley, 2002). Corrective maintenance, also known as run-to-failure maintenance, is a reactive management technique which is initiated only when a machine breaks down. On the other hand, preventive maintenance is performed before a machine malfunction. As one kind of condition-driven preventive maintenance, predictive maintenance determines the maintenance schedule of each machine by monitoring the mechanical condition, system efficiency, and other indicators. In contrast to the traditional preventive maintenance, which is only conducted on the same schedule every cycle, predictive maintenance is conducted as needed. Therefore, predictive maintenance often involves prognostic and health management (PHM), which is combining predictive analytics with condition-based monitoring. However, whether predictive maintenance should be conducted for a machine depends on not only the machine condition but also the current and future workload of the machine. The scheduling of predictive maintenance and job processing are thus interdependent.


In this study, we analyze both job scheduling and maintenance planning problems. Since the WSPT method usually can reach the optimal solution and spend less time than the mathematical model, we use the WSPT method to decide the job schedule. To consider the maintenance planning problem, we focus on PM planning, and our goal is to minimize total weighted completion time. Specifically, we use stochastic programming to model the problem and we model the uncertainty of machine failure as Weibull distribution. We compare our method with other baseline methods by evaluating saving. Moreover, we analyze the impact of parameters in the Weibull distribution and give insights and application examples to decision makers.

## 2. Methodology

In this section, we proposed a model to solve the job scheduling problem considering maintenance. There are two phases in our study. Frist, we use the rule **WSPT** to determine the job sequence, and second, based on the job sequence , we solve
the maintenance planning problem by our model using **stochastic programming**, which the objective is to minimize the total weighted completion time, to determine whether to implement PM between each job. We also compare our model with a periodical maintenance policy cited from Cassady and Kutanoglu (2015).

### 2.1 Job Scheduling

As mentioned above, we apply WSPT to decide the job sequence. For the ![formula](https://render.githubusercontent.com/render/math?math=i_{th}) job, we calculate the ratio ![formula](https://render.githubusercontent.com/render/math?math=\frac{w_i}{p_i}), where ![formula](https://render.githubusercontent.com/render/math?math={w_i}) is the weight and ![formula](https://render.githubusercontent.com/render/math?math={p_i}) is the processing time of the  job. We list the job sequence based on ![formula](https://render.githubusercontent.com/render/math?math={r_i}) since the higher the ![formula](https://render.githubusercontent.com/render/math?math={r_i}) is the more important the job should be done earlier.

### 2.2 PM Planning
#### The stochastic model
After listing the job by WSPT, we take the job sequence as an input of the next phase, which is the PM planning problem. The model is described as follows, where M is a large enough number.
![](https://i.imgur.com/rABC2Oe.png)
The objective value is to minimize the total weighted completion time. Constraints (1) define the expected value of completion time for the ![formula](https://render.githubusercontent.com/render/math?math=i_{th})job. Constraints (2) propose a cumulative distribution function of Weibull distribution according to a certain age of machine ![formula](https://render.githubusercontent.com/render/math?math={a_i}), and the function requires two parameters ![formula](https://render.githubusercontent.com/render/math?math={\beta}) and ![formula](https://render.githubusercontent.com/render/math?math={\eta}). Constraints (3) ensure that the age of the machine goes back to the minimum age after a PM happens. Constraints (4) define the recent age of the machine is the age of the machine after deciding maintenance plus the processing time of job ![formula](https://render.githubusercontent.com/render/math?math={j}). Constraints (5) are some binary constraints.
However, the model we mention above is nonlinear and the Gurobi cannot solve when the model is over the third power. Therefore, we use the piecewise technique according to Chang (2002) and revise the linearization model below, where M is a large enough number.

#### The peicewise stochastic model

![](https://i.imgur.com/cedXuKs.png)


The objective value is to minimize the total weighted completion time. Constraints (7) define the expected value of completion time for the i_th job and replace the polynomial function into a piecewise function. Constraints (8) to (18) are some new constraints due to the piecewise process. Constraints (3) are some binary constraints for the decision variables due to the piecewise process. The rest of the constraints are the same as mentioned above. The notation of our model is listed below.
![](https://i.imgur.com/L23D4oo.png)
![](https://i.imgur.com/DtM5DFg.png)

## 3 Numerical Experiments

In this section, we present our experiment setting and also the performance of our model and baseline model. We also discuss the impact of Weibull distribution parameters to our method.

### 3.1 Experiment setting

We proposed a numerical experiment to evaluate the efficiency of the piecewise model. We set the maximum age of the machine to 100 and the initial age of the machine is uniformly distributed from 1 to 100. For each job, the weighted for completion time is uniformly distributed from 1 to 5 and the processing time is uniformly distributed from 1 to 50. A CM spends 5 periods of time, and a PM spends only 2 periods of time.
To evaluate the performance of our proposed model, we adopt three factors to analyze the performance under different circumstances. The first factor is the  number of jobs. The second and the third factor are the parameters of the Weibull distribution. We create 7 different scenarios to evaluate our model where each scenario we randomly generate 20 dataset and calculate the average saving of each scenario. The detailed setting of each scenario is listed in the table below.

![](https://i.imgur.com/Ymj7qpZ.png)

### 3.2 Evaluation method
To evaluate our model, we use ‘saving’ to check the performance. Saving is defined as the difference between the objective value of the target method and the objective value of the greedy method. Since in our case, we want to minimize the weighted time, the greedy method is sure to get the optimal minimized value for each scenario. The method that has the lower saving, showing it finds a solution that is close to the optimal minimized value and thus has better performance.

### 3.3 Experiment result

Our baseline method is the periodic maintenance method. Periodic maintenance method is cited from Cassady and Kutanoglu (2015), where we calculate a ![formula](https://render.githubusercontent.com/render/math?math={τ^*}) that could have the largest steady-state machine availability. Steady-state machine availability, named ![formula](https://render.githubusercontent.com/render/math?math={A(τ)}) is defined below. Based on the ![formula](https://render.githubusercontent.com/render/math?math={A(τ)}) , we differentiate the function to find the ![formula](https://render.githubusercontent.com/render/math?math={τ^*}) that maximize ![formula](https://render.githubusercontent.com/render/math?math={A(τ)}) . After finding ![formula](https://render.githubusercontent.com/render/math?math={τ}) , we only implement a PM only when the age of the machine is larger than ![formula](https://render.githubusercontent.com/render/math?math={τ}). 
![](https://i.imgur.com/CgEQcbW.png)

 
### 3.3.1 Saving

We first evaluate our method and the baseline method by using 7 scenarios, where each scenario has 20 datasets. We calculate the average saving of each scenario and also calculate the average saving of all scenarios. The result is shown in the following table.
 
![](https://i.imgur.com/G98pcP9.png)


The saving of our method outperforms the baseline method in all scenarios. The average saving of the baseline method is 33.097, and we can see that the saving is highest for the baseline method when we have more jobs. On the other hand, the average saving of our method is 0, meaning that in all scenarios, our method can find the optimal solution.

3.3.2 Impact of Weibull distribution setting
To see the impact of the two parameters of Weibull distribution, we calculate the sum of the number of PM in each position and also the total number of PM out of $20$ datasets for the four scenarios. 

![](https://i.imgur.com/AZYVpYL.png)


For the first parameter of Weibull distribution, ![formula](https://render.githubusercontent.com/render/math?math={\eta}), we can see that the total number of PM is much larger when ![formula](https://render.githubusercontent.com/render/math?math={\eta}) is smaller. This is because ![formula](https://render.githubusercontent.com/render/math?math={\eta}) control the expected number of failure ![formula](https://render.githubusercontent.com/render/math?math={m(τ)}). Since ![formula](https://render.githubusercontent.com/render/math?math={\eta}) is the denominator in the function, the expected number of failure ![formula](https://render.githubusercontent.com/render/math?math={m(τ)}) is higher when ![formula](https://render.githubusercontent.com/render/math?math={\eta}) is smaller. Therefore, it makes sense to plan more PM when ![formula](https://render.githubusercontent.com/render/math?math={\eta}) is smaller. 
The second parameter of Weibull distribution is ![formula](https://render.githubusercontent.com/render/math?math={\beta}). We can see that there is no big difference between the total number of PM when ![formula](https://render.githubusercontent.com/render/math?math={\beta}) changes. However, when ![formula](https://render.githubusercontent.com/render/math?math={\beta}) is larger, our method tends to set more PM in the later position. This is because ![formula](https://render.githubusercontent.com/render/math?math={\beta}) controls the distribution of ![formula](https://render.githubusercontent.com/render/math?math={m(τ)}) like Figure 1. When ![formula](https://render.githubusercontent.com/render/math?math={\beta}) is larger, the value of ![formula](https://render.githubusercontent.com/render/math?math={m(τ)}) increases slowly first but increases tremendously afterwards. As a result, it makes sense to set more PM behind when ![formula](https://render.githubusercontent.com/render/math?math={\beta}) is larger. 

![](https://i.imgur.com/jN2eTJu.png)

## 4 Conclusion
From the previous study, we get some insights about the problem of job scheduling considering maintenance planning problems. Based on those insights, we will provide some suggestions to the decision maker. 
The first insight is about the effect of the parameters in Weibull distribution. Different machines/companies should use different parameters of Weibull distribution to model the expected machine failure times, since different parameters have different meaning. (E.g. large beta means the machine is not likely to fail when the age of the machine is small but is likely fail many times when it gets older). 
Second, the SP can achieve better performance than periodic maintenance since it considers more conditions and adjust the PM schedule to get better results. However, since the mathematical model usually costs more time to solve the problem, one should also consider the time spent for each method to make the final decision when having more jobs and more machines. Though the numerical experiment proves that our model reaches the optimal solution in all the cases in a reasonable time, we might have hundreds of jobs waiting to be scheduled in the real manufacturing process and the model we proposed may cost lots of time. So consider the cost of time usage is also very important. 
For the future work, we think one can consider more variable, such as the demand fluctuation or raw material supply when decide job scheduling and maintenance planning. Therefore, to design an efficient algorithm to solve this complex problem is still a further topic to study.  

## 5 Reference

Cassady, C. R., & Kutanoglu, E. (2005). Integrating preventive maintenance planning and production scheduling for a single machine. IEEE Transactions on reliability, 54(2), 304-309.

Li, H. L., Chang, C. T., & Tsai, J. F. (2002). Approximately global optimization for assortment problems using piecewise linearization techniques. European Journal of Operational Research, 140(3), 584-589.

Mobley, R.K. 2002. An introduction to predictive maintenance. Elsevier.

https://en.wikipedia.org/wiki/Weibull_distribution



