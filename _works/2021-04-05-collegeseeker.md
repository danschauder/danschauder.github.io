---
title: College Seeker - Visualizing College similarity and Predicting Student Debt
description: Using advanced analytics to improve college decisionmaking
category: Data Visualization
date: 2021-12-16 08:01:35 +0300
#role: Data Scientist
image: '/images/collegeseeker/college_graph.png'
image_caption: 'Masters project by Austin Krauss, Sanjana Kumar, Stephen Mullaly, Dan Schauder, and Matt Schlosser'
---

# Introduction and Motivation

* College choice is a critical life decision, yet no existing website allows students to easily discover and compare US universities while evaluating the potential debt burden they may incur from attending those universities.
* Our web app, [collegeseeker.net](https://collegeseeker.net/), seeks to fill this need and has four principal components:
	* A graph showcasing the similarity of a college to other, potentially unknown colleges based on different filter options.
	* Detailed statistics displayed in real time as an easily readable paragraph with a link to the college's website.
	* Information about feasibility of student loan repayment created using a linear regression model.
	* The most popular majors at each university displayed as a word cloud.

<img src="/images/collegeseeker/college_seeker_example.png" alt="Example College Seeker Session" />
*Sample instance of College Seeker, currently highlighting University of Michigan at Ann Arbor*

# Data

* 1,735 college logos using Clearbit API
* US Department of Education's College Scorecard database
    * Contains details on college locations, programs of study, enrollment, tuition, student demographics, loan repayment rates, and more
    * 168 MB CSV file consisting of 2,384 columns and 6,806 rows

# Analytical Approaches

## Discovery Graph

* Our team built a similarity network in which each college is a node. Any 2 colleges are connected by an edge with a weight given by the "similarity score" of the college pair.
* The similarity scores are created by mapping each university into a vector space using variables like geographic location, size, degrees offered, and average SAT score. Then, we calculated the similarity scores as the pairwise distances between university vectors. After experimenting with several distance metrics, we settled on using the Minkowski distance with a p-norm of 2.
* 