# T-squared (Hotelling's T²)T-squared (Hotelling's T²) method for Multivariate Control Charts

The T-squared (Hotelling's T²) method for Multivariate Control Charts is an extension of Shewhart control charts to monitor and control processes based on multiple interrelated quality variables. Unlike the traditional Shewhart charts that are univariate, focusing on a single quality characteristic at a time, the T-squared method allows for simultaneous monitoring of several related quality characteristics. This approach is particularly useful in situations where the quality of a process or product depends on more than one variable, and those variables may be correlated.

## Overview of the T-squared Method:

The T-squared method is based on Hotelling's T² statistic, which is a multivariate equivalent of the univariate Z-score. It provides a way to test hypotheses about means of multivariate normal distributions. In the context of SPC, the T² statistic is used to determine if a set of multivariate measurements is statistically similar to a set of control measurements. The steps to develop a Multivariate Control Chart using the T-squared method are as follows:

1. Data Collection and Preparation:
    * Collect sample data for the multivariate process. Each sample should include measurements for all variables being monitored.
    * Ensure data is normalized if scales of measurements differ significantly among variables.

2. Calculate the Sample Mean and Covariance Matrix:
    * Calculate the mean vector and the covariance matrix from the historical process data (the phase I dataset), which is assumed to be in control.

3. Compute the T-squared Statistic for New Observations:
    * For a new sample (a vector of measurements for the monitored variables), calculate the T-squared statistic using the formula:
    T2=(x−xˉ)′S−1(x−xˉ)
    T2=(x−xˉ)′S−1(x−xˉ)  
    where:
        * xx is the vector of new observations,
        * xˉxˉ is the mean vector of the in-control process,
        * S−1S−1 is the inverse of the covariance matrix of the in-control process,
        * T2T2 is the Hotelling's T-squared statistic.

4. Determine Control Limits:
    * The upper control limit (UCL) for the T-squared chart can be determined using the χ2χ2 (chi-square) distribution:
    UCL=(p⋅(n−1)⋅(n+1))n⋅(n−p)⋅Fα,p,n−p
    UCL=n⋅(n−p)(p⋅(n−1)⋅(n+1))​⋅Fα,p,n−p​  
    where:
        * pp is the number of variables,
        * nn is the sample size,
        * Fα,p,n−pFα,p,n−p​ is the F-distribution value at a chosen αα significance level, with degrees of freedom pp and n−pn−p.

5. Plot and Monitor the Chart:
    * Plot the T-squared statistic for each new set of measurements against the UCL.
    * Investigate any points that exceed the UCL as potential signals that the process may be out of control.

## Application:

The T-squared method is widely used in industries where quality is defined by multiple interrelated variables, such as manufacturing complex components requiring tight tolerances on multiple dimensions, chemical process industries where product quality depends on a combination of factors, or any process where monitoring multiple variables simultaneously can provide a more comprehensive view of process stability and capability.

This approach offers a powerful tool for quality control in multivariate processes, enabling the detection of out-of-control conditions that might not be identified by monitoring variables individually. However, it requires a solid understanding of multivariate statistics and careful consideration of the relationships among the variables being monitored.
