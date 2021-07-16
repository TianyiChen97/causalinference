# causalinference
How circadian misalignment affect people's health?

This project was progressed by Tianyi Chen; Jake Moon, Peiheng Gao; Jingyi Ren; Yiwen Chen and supervised by Professor Tom Woolf. This is a work summary written by Tianyi Chen on JULY 15th 2021.  

This project is a follow-up work of:”Machine Learning to Summarize and Provide Context for Sleep and Eating Schedules”(https://www.biorxiv.org/content/10.1101/2020.12.31.424983v1). Now with the summarized sleeping and eating schedules, we wonder how these schedules can affect people’s health. Thus naturally we need people’s schedules data(collected from our APP Daily24) and their electronic health record(EHR) data. 

Since EHR data contains privacy information, we started instead with synthetic EHR data； and as for schedules data, we also simply summarized each patient with circadian misalignment or not(in the future we can easily extend this to a circadian misalignment index using our previous paper’s result).

# Data Preparation:
Thus we have the dataset: containing two parts, one part is patients’ EHR with circadian misalignment, the other patients’ EHR without circadian misalignment.
Since EHR data is really complex and contains much information, we need to extract some essential information from it. And our next step is using deconfounder to do the analysis, which is a non-time series method, so we processed the data. You can find the R code in github. What we did is summarize each patient’s medication/encounters/observations across time into several variables such as: first time encounter(days from patient’s birthday), mean time encounter, last time encounter and total encounter times. It is similar to Breast cancer wisconsin (diagnostic) dataset, which is from deconfounder turial.  

# Deconfounder:
Now we can start our analysis. To explore how misalignment can affect patients’ health, we choose to use causal inference. We can use something as simple as linear regression, but causal inference is a finer way. 
Our main causal inference method is deconfounder, because it is simple and easy to code. The original paper is:https://arxiv.org/abs/1805.06826. Also there is a very nice tutorial for us to learn how to code deconfounder: https://github.com/blei-lab/deconfounder_tutorial.
I worte my code based on this:(https://colab.research.google.com/drive/1gs5v64H-YvUbB09-8Z4ff_t-QQR9KiFT?usp=sharing). You can use non-time series data_notcorrelated.csv to run the code. 

# Current problem:
as you can see from my code, we have initially run the code through and get the result, but in their original code, they standardized the data( for each column subtract the mean of column and divided by sd of column) at the beginning saying it is better for PCA model. This makes our interpretation of coefficients no sense. Since our value is a relative time for each patient, average across the population makes not much sense. We are considering solving this problem by: simply not standardizing the data. But i am not sure how much it will affect our PCA model. 
