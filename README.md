# Data Maturity Ranker Algorithm

This is a university project whose primary purpose is to develop a preliminary NLP-based assessment to analyse different companies on how data-mature they are, using a proprietary Data Maturity Model (DMM). This project will not give the company a metric on how data mature it is, because the metric derived
for a single company is not meant to be used only for the company. Instead, it will push towards
comparing to other companies and how to gain a lead in comparison to the other companies
analysed.

This toy algorithm will be applied towards 5 major ASX companies with different sectors to compare and contrast their approach. They are:
- CSL Limited (CSL),
- Commonwealth Bank of Australia (CBA),
- BHP Group (BHP),
- Woolworths Group (WOW), and
- Telstra Corporation (TLS).

# Project Significance
This project can be a very useful first step for students looking for data-related jobs to conduct some exploratory analysis on the job marketplace, and can be used to build a more mature database of companies in Australia, or anywhere else in the world.

# Project Methodology
Since the project offers a fresh perspective on companies’ data maturity that has not been established elsewhere, the challenges are to:
- Create a Data Maturity Model (DMM) that most effectively captures the current state of data
maturity in a company, while only using publicly available information.
- Create a scoring model and benchmark, based on the proposed DMM. The goal is not to
assess a single company based on a score, but rather to compare different companies based
on the relative range of scores.
- Create different datasets by means of scraping and modelling, to arrive at scoring results that
can be easily interpreted.

<img width="482" alt="pipeline" src="https://github.com/user-attachments/assets/39b99a33-61c4-47cf-8f07-c53d5b2b9969">

# The Data Maturity Model
The simple model to assess a company's data maturity is based on 6 key steps, the higher up the more mature.

1. Data collection
2. Data quality
3. Data analysis
4. Data culture
5. Data governance
6. Data monetisation

The unique challenge to this NLP project is the fact that only text data can be used to build the algorithm. Therefore, the proposed model consists of the following components:

1. A data dictionary, which is built manually, and reflects the vocabulary that is typical of each stage in the data maturity model.
2. The categories from which to collect data, which is hand-picked from the following:

- *Annual Reports*: these summarise company activities, and are likely to include their data approaches to problem solving and project implementation.
- *Privacy Policy*: these contain explicit data practices within the company, with more preference on user data.
- *Job postings*: data- and analytics-related positions carry information about how the company handles and utilises their data.
- *Social media updates*: these potentially show data-related projects that the company implemented, or will implement in the future.

3. The scoring system, which is primarily based on the Term Frequency - Inverse Document Frequency (TF-IDF) metric widely used within the NLP space. TF-IDF scoring system consisting of the following steps:

- A CorEx topic model to cluster the original dataset into data- and non-data-related texts. The CorEx algorithm is chosen for it provides a semi-supervised method to fitting and transforming data by letting users specify ‘anchored’ keywords for each topic and the associated importance it should learn, hence guiding the model (but not forcing it) to converge towards those prior topics like Latent Dirichlet Allocation, which is purely unsupervised. The anchored keywords were based on the data dictionary defined previously, because this dictionary represents the prior knowledge that we have about data maturity, which is then updated similar to a Bayesian model.
- Take the output of the previous step, and run it through a second CorEx topic model, producing six clusters representing the six data maturity stages. This model produces a binary topic likelihood matrix of shape `(n_samples, n_clusters)`, where each row (sample) encodes information about whether this sample is likely to belong to a particular cluster. Each sample may belong to multiple clusters, so a single row of this matrix can be, for instance, `[0, 1, 0, 0, 1, 0]`, denoting that this sample likely belongs to stages two and five of the data maturity model.
- Retrieve the learned binary topic likelihood matrix from the previous step, and filter it by company, and by data maturity stage. Two vocabulary versions are learned for two datasets:
(a) all data-related texts dataset, and (b) data-related texts for a single company at a
single data maturity stage. A TF-IDF matrix is calculated only for dataset (a), because it
represents the scores computed for the whole collection of documents, from which the sub-
vocabulary learned in dataset (b) will be referenced. This reference will retrieve all the TF-IDF scores for a single company in a single data maturity stage, and it can be scaled up to find the scores for all companies in all data maturity stages.
- Once all TF-IDF scores are retrieved, they will be weighted by the number representing the
data maturity stage (i.e. 1 for collection,..., 6 for monetisation), and a single weighted average is calculated for each company. The weights represent importance, with later stages carrying higher weights.
- The weighted average vector of length `(n_companies, )` should be used as a whole to
assess the relative data maturity ranking of different companies. The higher the score, the
more likely that that company is data mature; and the further apart two scores are, the more
data mature it is for the company with a higher score compared to the one with a lower score.

# Preliminary Results

This plot shows the unnormalised data maturity score matrix for each company considered.
<img width="633" alt="Screenshot 2024-09-02 at 8 43 54 PM" src="https://github.com/user-attachments/assets/4af70de8-ffc2-4155-ae33-3e508f0ca762">

`
company
BHP 1.023071
CBA 3.630520
CSL 0.021438
TLS 1.164261
WOW 4.408122
`

The raw scoring makes intuitive sense, and from these it's clear that Woolworths Group and Commonwealth Bank are clearly very mature in their data approach. 

# Limitations and Next steps

The scoring model is still in its early stage, and it's based on a single metric. It may not reflect all aspects of data maturity within a given company.

There was also quite some manual labour in data scraping, particularly in (legally) digging through HTML pages to find relevant contents for scraping.

However, this is a valuable first step to scaling up to become a mature database on different companies. Some next ideas may include:

- Construction of an index from which to look up a particular company's relative data maturity scoring in relation to other companies
- Deploying more advanced scoring algorithm as a more diverse range of data is added
- A full data pipeline to assess data maturity in an automated fashion.

LICENSE: MIT
