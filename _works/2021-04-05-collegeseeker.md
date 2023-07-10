---
title: College Seeker - Visualizing College Similarity and Evaluating Student Debt
description: Using advanced analytics to improve college decisionmaking
category: Data Visualization
date: 2021-12-16 08:01:35 +0300
#role: Data Scientist
image: '/images/collegeseeker/college_graph.png'
image_caption: 'Masters project by Austin Krauss, Sanjana Kumar, Stephen Mullaly, Dan Schauder, and Matt Schlosser'
---

# Introduction and Motivation

* College choice is a critical life decision, yet no existing website allows students to easily discover and compare US universities while evaluating the potential debt burden they may incur from attending those universities.
* Our team built a web app, [collegeseeker.net](https://collegeseeker.net/), to fill this need by providing:
	* A graph showcasing the similarity of a college to other, potentially unknown colleges based on different filter options.
	* Information about feasibility of student loan repayment based on historical repayment rates.
	* The most popular majors at each university displayed as a word cloud.
    * Detailed statistics displayed in an interactive UI with links to the college's website to learn more.

<img src="/images/collegeseeker/college_seeker_example.png" alt="Example College Seeker Session" />
*Sample instance of <a href="https://collegeseeker.net/" target="_blank">College Seeker</a>, currently highlighting University of Michigan at Ann Arbor*

# Data

* 1,735 college logos using Clearbit API
* US Department of Education's College Scorecard database
    * Contains details on college locations, programs of study, enrollment, tuition, student demographics, loan repayment rates, and more
    * 168 MB CSV file consisting of 2,384 columns and 6,806 rows

# Analytical Approaches

## Discovery Graph

* Our team built a similarity network in which each college is a node. Any 2 colleges are connected by an edge with a weight given by the "similarity score" of the college pair.
* To calculate similarity scores, first each university is mapped into a vector space using variables like geographic location, number of students, degrees offered, and average SAT score. Then, similarity scores are calculated as the pairwise distances between university vectors. After experimenting with several distance metrics, we settled on using the Minkowski distance with a p-norm of 2.
* Using this similarity network, for any given college, a <a href="https://collegeseeker.net/" target="_blank">College Seeker</a> user can easily discover other similar colleges to consider in their college search.

## Network Visualization

* With more than 1,700 universities included in the analysis, if all colleges were displayed at once, the view would be tangled and overwhelming.
* <a href="https://collegeseeker.net/" target="_blank">College Seeker</a> addresses this challenge by constraining the view to show the 15 nearest nighbors of a given university at a time.
* Upon double-clicking a neighboring university, its neighborhood is expanded, and the view pans and zooms to focus on the new neighbors that were revealed.
* Additionally, each node in the visualization is sized by student loan repayment rate associated with the university. More details including median student debt amount and loan repayment success rates are displayed upon clicking on a college logo.
* Lastly, the team generated word cloud images for all 1,735 colleges. When a <a href="https://collegeseeker.net/" target="_blank">College Seeker</a> user clicks on a university, the corresponding word cloud image is displayed to reveal the popular undergraduate programs of study offered at the university. Each program is sized by the proportion of undergraduate degrees awarded at the institution.

<img src="/images/collegeseeker/college_seeker_details_panel.png" alt="Example College Seeker Details Panel" />
*The details panel in the <a href="https://collegeseeker.net/" target="_blank">College Seeker</a> UI, displaying a description of the University of Texas at Austin, student debt information, and popular undergraduate programs of study*

# Experiment and Results

* After building the <a href="https://collegeseeker.net/" target="_blank">College Seeker</a> UI, our team conducted an experiment to evaluate its effectiveness.
* 104 college-bound juniors from McKinney Boyd High School in McKinney, Texas were randomly assigned to interact with either the <a href="https://collegeseeker.net/" target="_blank">College Seeker</a> website  or US News' college search website (after informed consent was obtained from parents).
* Each student was given 10 minutes to interact with their assigned website, then asked to complete a brief survey. The results of the survey are summarized here:

<img src="/images/collegeseeker/experiment_results.png" alt="College Seeker Experiment Results" />

* As shown above, when compared to the US News College Search website, <a href="https://collegeseeker.net/" target="_blank">College Seeker</a> users indicated they were more likely to discover more college options, more aware of loan repayment rates, more aware of popular majors at particular colleges, and more likely to recommend College Seeker for researching colleges.

# Conclusion

* In building <a href="https://collegeseeker.net/" target="_blank">College Seeker</a>, our team aimed to provide an intuitive way for prospective students to explore a pool of universities while taking student debt and loan repayment rates into account. Along the way, we had the opportunity to research and apply interesting analytics and web development techniques.