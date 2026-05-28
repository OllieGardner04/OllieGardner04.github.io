---
layout: default
title: "Project 2"
permalink: /projects/project-2/
markdown: kramdown
---

# Various approaches to estimating survival and recovery rates from dead recovery data

## Introduction

This research was inspired by a group project in my third year, in which we estimated the survival and recovery rates of blackbirds and sandwich terns from a BTO dead recovery dataset. We had access to ring recovery data, and used multinomial models to estimate the rates. At the start of the project, I misinterpreted the data, and ended up creating my own model that, while valid, produced incorrect results. After the project, I found the correct data, and fit a more appropriate model. I also fit the initial model on the new data. There is no benefit to fitting the initial model, but I present it as it's interesting. This page compares both models and contains the ongoing results of my research into this data.

I have not yet commented on the results of the Bayesian estimation or assessed goodness of fit.

## Background

Bird ringing is a method to track birds; a small ring is attached to their legs that can be used to uniquely identify them. If the bird dies and is found by someone and they report the death to the ringing scheme, then the ringers know that that bird has died. In a bird ringing scheme, a cohort of birds are ringed each year, and deaths in subsequent years from each cohort are recorded. This results in a dataset that looks like the table below

<div style="text-align: center; margin: 1em 0;">
  <table style="margin: 0 auto; border-collapse: collapse; width: auto;">
    <thead>
      <tr>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Year marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Number marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" colspan="4">$\text{Year recovered}$</th>
      </tr>
      <tr>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$1$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$2$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$3$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$4$</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td style="border: 1px solid #ccc;">  $1$</td>
        <td style="border: 1px solid #ccc;">  $N_{1}$</td>
        <td style="border: 1px solid #ccc;">  $R_{11}$</td>
        <td style="border: 1px solid #ccc;">  $R_{12}$</td>
        <td style="border: 1px solid #ccc;">  $R_{13}$</td>
        <td style="border: 1px solid #ccc;">  $R_{14}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $2$</td>
        <td style="border: 1px solid #ccc;">  $N_{2}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $R_{22}$</td>
        <td style="border: 1px solid #ccc;">  $R_{23}$</td>
        <td style="border: 1px solid #ccc;">  $R_{24}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $3$</td>
        <td style="border: 1px solid #ccc;">  $N_{3}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $R_{33}$</td>
        <td style="border: 1px solid #ccc;">  $R_{34}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $4$</td>
        <td style="border: 1px solid #ccc;">  $N_{4}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $R_{44}$</td>
      </tr>
    </tbody>
  </table>
</div>

For example, $R_{24}$ is the number of birds that were ringed in year 2 and recovered in year 4.

## Age-stratified data

As birds may survive at different rates at different ages, birds can be recorded as being ringed as an adult, juvenile, or pullus. In this research, a an adult is defined as being a bird older than 1 year of age, a juvenile is defined as being a bird less than 1 year old that has left the nest, and pullus is defined as being a bird still in the nest. By ringing birds as pulli, we can record whether they have died in the post-fledging period; this is the stage of life during which the bird has left the nest but is not yet independent of its parents. Birds are often highly vulnerable during this period. 

The 2 models discussed in this research estimate survival and reported expolitation rates during the post-fledging period, the juvenile period, and all subsequent years. The first model discussed uses data containing recoveries in the post-fledging period and juvenile period separately. The second model uses data where these counts are combined.

For both models, adult and juvenile data is the same, and is formatted as follows.

<div style="text-align: center; margin: 1em 0;">
  <table style="margin: 0 auto; border-collapse: collapse; width: auto;">
    <thead>
      <tr>
        <th colspan="6">Observed recovery counts of birds ringed as adults</th>
      </tr>
      <tr>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Year marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Number marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" colspan="4">$\text{Year recovered}$</th>
      </tr>
      <tr>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$1$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$2$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$3$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$4$</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td style="border: 1px solid #ccc;">  $1$</td>
        <td style="border: 1px solid #ccc;">  $N_{1}$</td>
        <td style="border: 1px solid #ccc;">  $R_{11}$</td>
        <td style="border: 1px solid #ccc;">  $R_{12}$</td>
        <td style="border: 1px solid #ccc;">  $R_{13}$</td>
        <td style="border: 1px solid #ccc;">  $R_{14}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $2$</td>
        <td style="border: 1px solid #ccc;">  $N_{2}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $R_{22}$</td>
        <td style="border: 1px solid #ccc;">  $R_{23}$</td>
        <td style="border: 1px solid #ccc;">  $R_{24}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $3$</td>
        <td style="border: 1px solid #ccc;">  $N_{3}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $R_{33}$</td>
        <td style="border: 1px solid #ccc;">  $R_{34}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $4$</td>
        <td style="border: 1px solid #ccc;">  $N_{4}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $R_{44}$</td>
      </tr>
    </tbody>
  </table>
</div>

<div style="text-align: center; margin: 1em 0;">
  <table style="margin: 0 auto; border-collapse: collapse; width: auto;">
    <thead>
      <tr>
        <th colspan="6">Observed recovery counts of birds ringed as juveniles</th>
      </tr>
      <tr>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Year marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Number marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" colspan="4">$\text{Year recovered}$</th>
      </tr>
      <tr>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$1$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$2$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$3$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$4$</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td style="border: 1px solid #ccc;">  $1$</td>
        <td style="border: 1px solid #ccc;">  $M_{1}$</td>
        <td style="border: 1px solid #ccc;">  $Q_{11}$</td>
        <td style="border: 1px solid #ccc;">  $Q_{12}$</td>
        <td style="border: 1px solid #ccc;">  $Q_{13}$</td>
        <td style="border: 1px solid #ccc;">  $Q_{14}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $2$</td>
        <td style="border: 1px solid #ccc;">  $M_{2}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $Q_{22}$</td>
        <td style="border: 1px solid #ccc;">  $Q_{23}$</td>
        <td style="border: 1px solid #ccc;">  $Q_{24}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $3$</td>
        <td style="border: 1px solid #ccc;">  $M_{3}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $Q_{33}$</td>
        <td style="border: 1px solid #ccc;">  $Q_{34}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $4$</td>
        <td style="border: 1px solid #ccc;">  $M_{4}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $Q_{44}$</td>
      </tr>
    </tbody>
  </table>
</div>

## Separate post-fledging recovery data

### The data

In the case where post-fledging recoveries are recorded separately to juvenile recoveries, the pullus recovery data will be stored as in the table below

<div style="text-align: center; margin: 1em 0;">
  <table style="margin: 0 auto; border-collapse: collapse; width: auto;">
    <thead>
      <tr>
        <th colspan="7">Observed recovery counts of birds ringed as pulli</th>
      </tr>
      <tr>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Year marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Number marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" colspan="5">$\text{Year recovered}$</th>
      </tr>
      <tr>
	<th style="border: 1px solid #ccc; padding: 6px 12px;" colspan="2">$1$</th>
	<th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="2">$2$</th>
	<th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="2">$3$</th>
	<th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="2">$4$</th>
      </tr>
      <tr>
        <th style="border: 1px solid #ccc; padding: 6px 12px; background-color: rgba(255, 0, 0, 0.5);">$\text{PF}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px; background-color: rgba(0, 0, 255, 0.5);">$\text{Juv.}$</th>	
      </tr>
    </thead>
    <tbody>
      <tr>
        <td style="border: 1px solid #ccc;">  $1$</td>
        <td style="border: 1px solid #ccc;">  $L_{1}$</td>
        <td style="border: 1px solid #ccc; background-color: rgba(255, 0, 0, 0.5);">  $P_{11}$</td>
        <td style="border: 1px solid #ccc; background-color: rgba(0, 0, 255, 0.5);">  $P_{12}$</td>
        <td style="border: 1px solid #ccc;">  $P_{13}$</td>
        <td style="border: 1px solid #ccc;">  $P_{14}$</td>
        <td style="border: 1px solid #ccc;">  $P_{15}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $2$</td>
        <td style="border: 1px solid #ccc;">  $L_{2}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc; background-color: rgba(255, 0, 0, 0.5);">  $P_{22}$</td>
        <td style="border: 1px solid #ccc; background-color: rgba(0, 0, 255, 0.5);">  $P_{23}$</td>
        <td style="border: 1px solid #ccc;">  $P_{24}$</td>
        <td style="border: 1px solid #ccc;">  $P_{25}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $3$</td>
        <td style="border: 1px solid #ccc;">  $L_{3}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc; background-color: rgba(255, 0, 0, 0.5);">  $P_{33}$</td>
        <td style="border: 1px solid #ccc; background-color: rgba(0, 0, 255, 0.5);">  $P_{34}$</td>
        <td style="border: 1px solid #ccc;">  $P_{35}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $4$</td>
        <td style="border: 1px solid #ccc;">  $L_{4}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc; background-color: rgba(255, 0, 0, 0.5);">  $P_{44}$</td>
        <td style="border: 1px solid #ccc; background-color: rgba(0, 0, 255, 0.5);">  $P_{45}$</td>
      </tr>
    </tbody>
  </table>
</div>

The first cell in each row is the number of birds from cohort $i$ recovered in their post-fledging period; the second cell in each row is the number of birds from cohort $i$ recovered in their first year after their post-fledging period.

### The model

Initially, I consider a likelihood based approach. Under the Brownie parameterisation, we estimate $S$, the surival rate and $f$, the harvest rate, also called the reported expolitation rate. $f$ is defined as $(1-S)r$, the probability of not surviving <i>and</i> being recovered and reported. I allow these parameters to vary over time, so that $S_{i}$ is the survival rate in the $i$th year of the study, and $f_{i} = (1-S_{i})r_{i}$ is the probability of dying and being recovered and reported in the $i$th year.

Under the Seber parameteristaion, we estimate $S$ and $r$. I primarily present the Brownie parameterisation, as deriving the MLEs is simpler this way. I present results from both parameterisations.

The data is modelled as coming from a multinomial distribution. I assign one set of parameters for each year of an adults life, a separate set for the first year of life for birds ringed as juveniles, and a separate set for the post-fledging period for birds ringed as pulli.

Under this model, the expected number of recoveries in each cell of each table is given by

<div style="text-align: center; margin: 1em 0;">
  <table style="margin: 0 auto; border-collapse: collapse; width: auto;">
    <thead>
      <tr>
        <th colspan="6">Expected recovery counts of birds ringed as adults</th>
      </tr>
      <tr>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Year marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Number marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" colspan="4">$\text{Year recovered}$</th>
      </tr>
      <tr>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$1$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$2$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$3$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$4$</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td style="border: 1px solid #ccc;">  $1$</td>
        <td style="border: 1px solid #ccc;">  $N_{1}$</td>
        <td style="border: 1px solid #ccc;">  $N_{1}f_{1}$</td>
        <td style="border: 1px solid #ccc;">  $N_{1}S_{1}f_{2}$</td>
        <td style="border: 1px solid #ccc;">  $N_{1}S_{1}S_{2}f_{3}$</td>
        <td style="border: 1px solid #ccc;">  $N_{1}S_{1}S_{2}S_{3}f_{4}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $2$</td>
        <td style="border: 1px solid #ccc;">  $N_{2}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $N_{2}f_{2}$</td>
        <td style="border: 1px solid #ccc;">  $N_{2}S_{2}f_{3}$</td>
        <td style="border: 1px solid #ccc;">  $N_{2}S_{2}S_{3}f_{4}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $3$</td>
        <td style="border: 1px solid #ccc;">  $N_{3}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $N_{3}f_{3}$</td>
        <td style="border: 1px solid #ccc;">  $N_{3}S_{3}f_{4}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $4$</td>
        <td style="border: 1px solid #ccc;">  $N_{4}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $N_{4}f_{4}$</td>
      </tr>
    </tbody>
  </table>
</div>

<div style="text-align: center; margin: 1em 0;">
  <table style="margin: 0 auto; border-collapse: collapse; width: auto;">
    <thead>
      <tr>
        <th colspan="6">Expected recovery counts of birds ringed as juveniles</th>
      </tr>
      <tr>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Year marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Number marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" colspan="4">$\text{Year recovered}$</th>
      </tr>
      <tr>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$1$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$2$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$3$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$4$</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td style="border: 1px solid #ccc;">  $1$</td>
        <td style="border: 1px solid #ccc;">  $M_{1}$</td>
        <td style="border: 1px solid #ccc;">  $M_{1}f_{1}' $</td>
        <td style="border: 1px solid #ccc;">  $M_{1}S_{1}' f_{2}$</td>
        <td style="border: 1px solid #ccc;">  $M_{1}S_{1}' S_{2}f_{3}$</td>
        <td style="border: 1px solid #ccc;">  $M_{1}S_{1}' S_{2}S_{3}f_{4}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $2$</td>
        <td style="border: 1px solid #ccc;">  $M_{2}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $M_{2}f_{2}' $</td>
        <td style="border: 1px solid #ccc;">  $M_{2}S_{2}' f_{3}$</td>
        <td style="border: 1px solid #ccc;">  $M_{2}S_{2}' S_{3}f_{4}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $3$</td>
        <td style="border: 1px solid #ccc;">  $M_{3}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $M_{3}f_{3}' $</td>
        <td style="border: 1px solid #ccc;">  $M_{3}S_{3}' f_{4}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $4$</td>
        <td style="border: 1px solid #ccc;">  $M_{4}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $M_{4}f_{4}' $</td>
      </tr>
    </tbody>
  </table>
</div>

<div style="text-align: center; margin: 1em 0;">
  <table style="margin: 0 auto; border-collapse: collapse; width: auto;">
    <thead>
      <tr>
        <th colspan="7">Expected recovery counts of birds ringed as pulli</th>
      </tr>
      <tr>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Year marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Number marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" colspan="5">$\text{Year recovered}$</th>
      </tr>
      <tr>
	<th style="border: 1px solid #ccc; padding: 6px 12px;" colspan="2">$1$</th>
	<th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="2">$2$</th>
	<th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="2">$3$</th>
	<th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="2">$4$</th>
      </tr>
      <tr>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$\text{PF}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$\text{Juv.}$</th>	
      </tr>
    </thead>
    <tbody>
      <tr>
        <td style="border: 1px solid #ccc;">  $1$</td>
        <td style="border: 1px solid #ccc;">  $L_{1}$</td>
        <td style="border: 1px solid #ccc;">  $L_{1}f_{1}'' $</td>
        <td style="border: 1px solid #ccc;">  $L_{1}S_{1}'' f_{1}' $</td>
        <td style="border: 1px solid #ccc;">  $L_{1}S_{1}'' S_{1}' f_{2}$</td>
        <td style="border: 1px solid #ccc;">  $L_{1}S_{1}'' S_{1}' S_{2}f_{3}$</td>
        <td style="border: 1px solid #ccc;">  $L_{1}S_{1}'' S_{1}' S_{2}S_{3}f_{4}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $2$</td>
        <td style="border: 1px solid #ccc;">  $L_{2}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $L_{2}f_{2}'' $</td>
        <td style="border: 1px solid #ccc;">  $L_{2}S_{2}'' f_{2}' $</td>
        <td style="border: 1px solid #ccc;">  $L_{2}S_{2}'' S_{2}' f_{3}$</td>
        <td style="border: 1px solid #ccc;">  $L_{2}S_{2}'' S_{2}' S_{3}f_{4}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $3$</td>
        <td style="border: 1px solid #ccc;">  $L_{3}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $L_{3}f_{3}'' $</td>
        <td style="border: 1px solid #ccc;">  $L_{3}S_{3}'' f_{3}' $</td>
        <td style="border: 1px solid #ccc;">  $L_{3}S_{3}'' S_{3}' f_{4}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $4$</td>
        <td style="border: 1px solid #ccc;">  $L_{4}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $L_{4}f_{4}'' $</td>
        <td style="border: 1px solid #ccc;">  $L_{4}S_{4}'' f_{4}' $</td>
      </tr>
    </tbody>
  </table>
</div>

Details on how I use the Seber parameterisation are below

<details>
<summary>Seber parameterisation</summary>

<div style="text-align: center; margin: 1em 0;">
  <table style="margin: 0 auto; border-collapse: collapse; width: auto;">
    <thead>
      <tr>
        <th colspan="6">Expected recovery counts of birds ringed as adults</th>
      </tr>
      <tr>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Year marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Number marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" colspan="4">$\text{Year recovered}$</th>
      </tr>
      <tr>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$1$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$2$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$3$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$4$</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td style="border: 1px solid #ccc;">  $1$</td>
        <td style="border: 1px solid #ccc;">  $N_{1}$</td>
        <td style="border: 1px solid #ccc;">  $N_{1} (1 - S_{1})r_{1}$</td>
        <td style="border: 1px solid #ccc;">  $N_{1}S_{1} (1 - S_{2})r_{2}$</td>
        <td style="border: 1px solid #ccc;">  $N_{1}S_{1}S_{2} (1 - S_{3})r_{3}$</td>
        <td style="border: 1px solid #ccc;">  $N_{1}S_{1}S_{2}S_{3} (1 - S_{4})r_{4}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $2$</td>
        <td style="border: 1px solid #ccc;">  $N_{2}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $N_{2} (1 - S_{2})r_{2}$</td>
        <td style="border: 1px solid #ccc;">  $N_{2}S_{2} (1 - S_{3})r_{3}$</td>
        <td style="border: 1px solid #ccc;">  $N_{2}S_{2}S_{3} (1 - S_{4})r_{4}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $3$</td>
        <td style="border: 1px solid #ccc;">  $N_{3}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $N_{3} (1 - S_{3})r_{3}$</td>
        <td style="border: 1px solid #ccc;">  $N_{3}S_{3} (1 - S_{4})r_{4}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $4$</td>
        <td style="border: 1px solid #ccc;">  $N_{4}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $N_{4} (1 - S_{4})r_{4}$</td>
      </tr>
    </tbody>
  </table>
</div>

<div style="text-align: center; margin: 1em 0;">
  <table style="margin: 0 auto; border-collapse: collapse; width: auto;">
    <thead>
      <tr>
        <th colspan="6">Expected recovery counts of birds ringed as juveniles</th>
      </tr>
      <tr>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Year marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Number marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" colspan="4">$\text{Year recovered}$</th>
      </tr>
      <tr>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$1$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$2$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$3$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$4$</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td style="border: 1px solid #ccc;">  $1$</td>
        <td style="border: 1px solid #ccc;">  $M_{1}$</td>
        <td style="border: 1px solid #ccc;">  $M_{1}(1 - S_{1}')r_{1}' $</td>
        <td style="border: 1px solid #ccc;">  $M_{1}S_{1}' (1 - S_{2})r_{2}$</td>
        <td style="border: 1px solid #ccc;">  $M_{1}S_{1}' S_{2} (1 - S_{3})r_{3}$</td>
        <td style="border: 1px solid #ccc;">  $M_{1}S_{1}' S_{2}S_{3} (1 - S_{4})r_{4}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $2$</td>
        <td style="border: 1px solid #ccc;">  $M_{2}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $M_{2} (1 - S_{2}')r_{2}' $</td>
        <td style="border: 1px solid #ccc;">  $M_{2}S_{2}' (1 - S_{3})r_{3}$</td>
        <td style="border: 1px solid #ccc;">  $M_{2}S_{2}' S_{3} (1 - S_{4})r_{4}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $3$</td>
        <td style="border: 1px solid #ccc;">  $M_{3}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $M_{3} (1 - S_{3}')r_{3}' $</td>
        <td style="border: 1px solid #ccc;">  $M_{3}S_{3}' (1 - S_{4})r_{4}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $4$</td>
        <td style="border: 1px solid #ccc;">  $M_{4}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $M_{4} (1 - S_{4}')r_{4}' $</td>
      </tr>
    </tbody>
  </table>
</div>

<div style="text-align: center; margin: 1em 0;">
  <table style="margin: 0 auto; border-collapse: collapse; width: auto;">
    <thead>
      <tr>
        <th colspan="7">Expected recovery counts of birds ringed as pulli</th>
      </tr>
      <tr>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Year marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Number marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" colspan="5">$\text{Year recovered}$</th>
      </tr>
      <tr>
	<th style="border: 1px solid #ccc; padding: 6px 12px;" colspan="2">$1$</th>
	<th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="2">$2$</th>
	<th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="2">$3$</th>
	<th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="2">$4$</th>
      </tr>
      <tr>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$\text{PF}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$\text{Juv.}$</th>	
      </tr>
    </thead>
    <tbody>
      <tr>
        <td style="border: 1px solid #ccc;">  $1$</td>
        <td style="border: 1px solid #ccc;">  $L_{1}$</td>
        <td style="border: 1px solid #ccc;">  $L_{1}(1 - S_{1}'' )r_{1}'' $</td>
        <td style="border: 1px solid #ccc;">  $L_{1}S_{1}'' (1 - S_{1}')r_{1}' $</td>
        <td style="border: 1px solid #ccc;">  $L_{1}S_{1}'' S_{1}' (1 - S_{2})r_{2}$</td>
        <td style="border: 1px solid #ccc;">  $L_{1}S_{1}'' S_{1}' S_{2} (1 - S_{3})r_{3}$</td>
        <td style="border: 1px solid #ccc;">  $L_{1}S_{1}'' S_{1}' S_{2}S_{3} (1 - S_{4})r_{4}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $2$</td>
        <td style="border: 1px solid #ccc;">  $L_{2}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $L_{2}(1 - S_{2}'' )r_{2}'' $</td>
        <td style="border: 1px solid #ccc;">  $L_{2}S_{2}'' (1 - S_{2}')r_{2}' $</td>
        <td style="border: 1px solid #ccc;">  $L_{2}S_{2}'' S_{2}' (1 - S_{3})r_{3}$</td>
        <td style="border: 1px solid #ccc;">  $L_{2}S_{2}'' S_{2}' S_{3} (1 - S_{4})r_{4}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $3$</td>
        <td style="border: 1px solid #ccc;">  $L_{3}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $L_{3}(1 - S_{3}'' )r_{3}'' $</td>
        <td style="border: 1px solid #ccc;">  $L_{3}S_{3}'' (1 - S_{3}')r_{3}' $</td>
        <td style="border: 1px solid #ccc;">  $L_{3}S_{3}'' S_{3}' (1 - S_{4})r_{4}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $4$</td>
        <td style="border: 1px solid #ccc;">  $L_{4}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $L_{4}(1 - S_{4}'' )r_{4}'' $</td>
        <td style="border: 1px solid #ccc;">  $L_{4}S_{4}'' (1 - S_{4}')r_{4}' $</td>
      </tr>
    </tbody>
  </table>
</div>

Notice that $(1 - S_{4})r_{4}$ and $(1 - S_{4}')r_{4}'$ only appear as a product, thus making $S_{4}$, $r_{4}$, $S_{4}'$, and $r_{4}'$ unidentifiable. $S_{4}''$ appears in 2 separate places, meaning that it <i>is</i> identifiable. This means that $r_{4}''$ is also identifiable. To deal with the parameters that can't be identified, I use the Brownie parameterisation for these parameters. Therefore, the tables become

<div style="text-align: center; margin: 1em 0;">
  <table style="margin: 0 auto; border-collapse: collapse; width: auto;">
    <thead>
      <tr>
        <th colspan="6">Expected recovery counts of birds ringed as adults</th>
      </tr>
      <tr>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Year marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Number marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" colspan="4">$\text{Year recovered}$</th>
      </tr>
      <tr>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$1$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$2$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$3$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$4$</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td style="border: 1px solid #ccc;">  $1$</td>
        <td style="border: 1px solid #ccc;">  $N_{1}$</td>
        <td style="border: 1px solid #ccc;">  $N_{1} (1 - S_{1})r_{1}$</td>
        <td style="border: 1px solid #ccc;">  $N_{1}S_{1} (1 - S_{2})r_{2}$</td>
        <td style="border: 1px solid #ccc;">  $N_{1}S_{1}S_{2} (1 - S_{3})r_{3}$</td>
        <td style="border: 1px solid #ccc;">  $N_{1}S_{1}S_{2}S_{3} f_{4}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $2$</td>
        <td style="border: 1px solid #ccc;">  $N_{2}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $N_{2} (1 - S_{2})r_{2}$</td>
        <td style="border: 1px solid #ccc;">  $N_{2}S_{2} (1 - S_{3})r_{3}$</td>
        <td style="border: 1px solid #ccc;">  $N_{2}S_{2}S_{3} f_{4}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $3$</td>
        <td style="border: 1px solid #ccc;">  $N_{3}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $N_{3} (1 - S_{3})r_{3}$</td>
        <td style="border: 1px solid #ccc;">  $N_{3}S_{3} f_{4}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $4$</td>
        <td style="border: 1px solid #ccc;">  $N_{4}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $N_{4} f_{4}$</td>
      </tr>
    </tbody>
  </table>
</div>

<div style="text-align: center; margin: 1em 0;">
  <table style="margin: 0 auto; border-collapse: collapse; width: auto;">
    <thead>
      <tr>
        <th colspan="6">Expected recovery counts of birds ringed as juveniles</th>
      </tr>
      <tr>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Year marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Number marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" colspan="4">$\text{Year recovered}$</th>
      </tr>
      <tr>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$1$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$2$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$3$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$4$</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td style="border: 1px solid #ccc;">  $1$</td>
        <td style="border: 1px solid #ccc;">  $M_{1}$</td>
        <td style="border: 1px solid #ccc;">  $M_{1}(1 - S_{1}')r_{1}' $</td>
        <td style="border: 1px solid #ccc;">  $M_{1}S_{1}' (1 - S_{2})r_{2}$</td>
        <td style="border: 1px solid #ccc;">  $M_{1}S_{1}' S_{2} (1 - S_{3})r_{3}$</td>
        <td style="border: 1px solid #ccc;">  $M_{1}S_{1}' S_{2}S_{3} f_{4}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $2$</td>
        <td style="border: 1px solid #ccc;">  $M_{2}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $M_{2} (1 - S_{2}')r_{2}' $</td>
        <td style="border: 1px solid #ccc;">  $M_{2}S_{2}' (1 - S_{3})r_{3}$</td>
        <td style="border: 1px solid #ccc;">  $M_{2}S_{2}' S_{3} f_{4}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $3$</td>
        <td style="border: 1px solid #ccc;">  $M_{3}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $M_{3} (1 - S_{3}')r_{3}' $</td>
        <td style="border: 1px solid #ccc;">  $M_{3}S_{3}' f_{4}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $4$</td>
        <td style="border: 1px solid #ccc;">  $M_{4}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $M_{4} f_{4}' $</td>
      </tr>
    </tbody>
  </table>
</div>

<div style="text-align: center; margin: 1em 0;">
  <table style="margin: 0 auto; border-collapse: collapse; width: auto;">
    <thead>
      <tr>
        <th colspan="7">Expected recovery counts of birds ringed as pulli</th>
      </tr>
      <tr>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Year marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Number marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" colspan="5">$\text{Year recovered}$</th>
      </tr>
      <tr>
	<th style="border: 1px solid #ccc; padding: 6px 12px;" colspan="2">$1$</th>
	<th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="2">$2$</th>
	<th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="2">$3$</th>
	<th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="2">$4$</th>
      </tr>
      <tr>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$\text{PF}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$\text{Juv.}$</th>	
      </tr>
    </thead>
    <tbody>
      <tr>
        <td style="border: 1px solid #ccc;">  $1$</td>
        <td style="border: 1px solid #ccc;">  $L_{1}$</td>
        <td style="border: 1px solid #ccc;">  $L_{1}(1 - S_{1}'' )r_{1}'' $</td>
        <td style="border: 1px solid #ccc;">  $L_{1}S_{1}'' (1 - S_{1}')r_{1}' $</td>
        <td style="border: 1px solid #ccc;">  $L_{1}S_{1}'' S_{1}' (1 - S_{2})r_{2}$</td>
        <td style="border: 1px solid #ccc;">  $L_{1}S_{1}'' S_{1}' S_{2} (1 - S_{3})r_{3}$</td>
        <td style="border: 1px solid #ccc;">  $L_{1}S_{1}'' S_{1}' S_{2}S_{3} f_{4}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $2$</td>
        <td style="border: 1px solid #ccc;">  $L_{2}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $L_{2}(1 - S_{2}'' )r_{2}'' $</td>
        <td style="border: 1px solid #ccc;">  $L_{2}S_{2}'' (1 - S_{2}')r_{2}' $</td>
        <td style="border: 1px solid #ccc;">  $L_{2}S_{2}'' S_{2}' (1 - S_{3})r_{3}$</td>
        <td style="border: 1px solid #ccc;">  $L_{2}S_{2}'' S_{2}' S_{3} f_{4}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $3$</td>
        <td style="border: 1px solid #ccc;">  $L_{3}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $L_{3}(1 - S_{3}'' )r_{3}'' $</td>
        <td style="border: 1px solid #ccc;">  $L_{3}S_{3}'' (1 - S_{3}')r_{3}' $</td>
        <td style="border: 1px solid #ccc;">  $L_{3}S_{3}'' S_{3}' f_{4}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $4$</td>
        <td style="border: 1px solid #ccc;">  $L_{4}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $L_{4}(1 - S_{4}'' )r_{4}'' $</td>
        <td style="border: 1px solid #ccc;">  $L_{4}S_{4}'' f_{4}' $</td>
      </tr>
    </tbody>
  </table>
</div>

</details>

### The likelihood

This model gives the row-wise log-likelihood functions

$${::nomarkdown}
\begin{aligned}
\ln\left(\mathcal{L}_{p}\right) &\propto R_{pp}\ln(f_{p}) + R_{p(p+1)}\ln(S_{p}f_{p+1}) + \dots + R_{pi}\ln(S_{p}S_{p+1}\dots S_{i-1}f_{i}) + \\ &\quad R_{p(i+1)}\ln(S_{p}S_{p+1}\dots S_{i-1}S_{i}f_{i+1}) + \dots + R_{pl}\ln(S_{p}S_{p+1}\dots S_{l-1}f_{l}) + (N_{p} - R_{p.})\ln(1 - \theta_{p}) \\
\ln\left(\mathcal{L}'_{p}\right) &\propto Q_{pp}\ln(f'_{p}) + Q_{p(p+1)}\ln(S'_{p}f_{p+1}) + \dots + Q_{pi}\ln(S'_{p}S_{p+1}\dots S_{i-1}f_{i}) + \\ &\quad Q_{p(i+1)}\ln(S'_{p}S_{p+1}\dots S_{i-1}S_{i}f_{i+1}) + \dots + Q_{pl}\ln(S'_{p}S_{p+1}\dots S_{l-1}f_{l}) + (M_{p} - Q_{p.})\ln(1 - \theta'_{p}) \\
\ln\left(\mathcal{L}''_{p}\right) &\propto P_{pp}\ln(f''_{p}) + P_{p(p+1)}\ln(S''_{p}f'_{p}) + \dots + P_{p(i+1)}\ln(S''_{p}S'_{p}S_{p+1}\dots S_{i-1}f_{i}) + \\ &\quad P_{p(i+2)}\ln(S''_{p}S'_{p}S_{p+1}\dots S_{i-1}S_{i}f_{i+1}) + \dots + P_{p(l+1)}\ln(S''_{p}S'_{p}S_{p+1}\dots S_{l-1}f_{l}) + (L_{p} - P_{p.})\ln(1 - \theta''_{p})
\end{aligned}
{:/}$$

where

$${::nomarkdown}
\begin{aligned}
\theta_{p} &= f_{p} + S_{p}f_{p+1} + S_{p}S_{p+1}f_{p+2} + S_{p}S_{p+1}S_{p+2}f_{p+3} + \dots + \left(S_{p}S_{p+1}\dots S_{i-1}f_{i}\right) \\ &\quad + \left(S_{p}S_{p+1}\dots S_{i-1}S_{i}f_{i+1}\right) + \dots + \left(S_{p}S_{p+1}\dots S_{l-1}f_{l}\right) \\
\theta'_{p} &= f'_{p} + S'_{p}f_{p+1} + S'_{p}S_{p+1}f_{p+2} + S'_{p}S_{p+1}S_{p+2}f_{p+3} + \dots + \left(S'_{p}S_{p+1}\dots S_{i-1}f_{i}\right) \\ &\quad+ \left(S'_{p}S_{p+1}\dots S_{i-1}S_{i}f_{i+1}\right) + \dots + \left(S'_{p}S_{p+1}\dots S_{l-1}f_{l}\right) \\
\theta''_{p} &= f''_{p} + S''_{p}f'_{p} + S''_{p}S'_{p}f_{p+1} + S''_{p}S'_{p}S_{p+1}f_{p+2} + \dots + \left(S''_{p}S'_{p}S_{p+1}\dots S_{i-1}f_{i}\right) \\ &\quad + \left(S''_{p}S'_{p}S_{p+1}\dots S_{i-1}S_{i}f_{i+1}\right) + \dots + \left(S''_{p}S'_{p}S_{p+1}\dots S_{l-1}f_{l}\right)
\end{aligned}
{:/}$$

are the total probabilities of recovery for cohort $p$. From this, we define the total log-likelihoods for each age group

$${::nomarkdown}
\begin{aligned}
\ln\left(\mathcal{L}\right) &= \sum_{i=1}^{l}\mathcal{L}_{i} \\
\ln\left(\mathcal{L'}\right) &= \sum_{i=1}^{l}\mathcal{L'}_{i} \\
\ln\left(\mathcal{L''}\right) &= \sum_{i=1}^{l}\mathcal{L''}_{i} \\
\end{aligned}
{:/}$$

so that the total log-likelihood is given by

$${::nomarkdown}
\mathscr{L} = \ln\left(\mathcal{L}\right) + \ln\left(\mathcal{L'}\right) + \ln\left(\mathcal{L''}\right)
{:/}$$

### MLEs

Let

$${::nomarkdown}
\begin{aligned}
T_{1} &= R_{1.} \\
U_{1} &= Q_{1.} \\
V_{1} &= P_{1.} \\
T_{i} &= R_{i.} + T_{i-1} - R_{.i} \\
U_{i} &= Q_{i.} + U_{i-1} - Q_{.i} \\
V_{i} &= P_{i.} + V_{i-1} - P_{.i}
\end{aligned}
{:/}$$

where $R_{i.}$ and $R_{.i}$ are row and column sums of observed adult recoveries respectively. $Q_{i.}$, $Q_{.i}$, $P_{i.}$, and $P_{.i}$ are defined the same for their respective tables.

Visually, these represent the sums of all points within "rectangles" with a bottom left corner on the main diagonal. 

Below is an example of observed counts of recovered adults, with $T_{2}$ highlighted.

<div style="text-align: center; margin: 1em 0;">
  <table style="margin: 0 auto; border-collapse: collapse; width: auto;">
    <thead>
      <tr>
        <th colspan="7">Observed recovery counts of birds ringed as adults</th>
      </tr>
      <tr>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Year marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Number marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" colspan="4">$\text{Year recovered}$</th>
		<th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Row totals}$</th>
      </tr>
      <tr>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$1$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$2$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$3$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$4$</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td style="border: 1px solid #ccc;">  $1$</td>
        <td style="border: 1px solid #ccc;">  $N_{1}$</td>
        <td style="border: 1px solid #ccc;">  $R_{11}$</td>
        <td style="border: 1px solid #ccc; background-color: rgba(0, 255, 0, 0.5);">  $R_{12}$</td>
        <td style="border: 1px solid #ccc; background-color: rgba(0, 255, 0, 0.5);">  $R_{13}$</td>
        <td style="border: 1px solid #ccc; background-color: rgba(0, 255, 0, 0.5);">  $R_{14}$</td>
		<td style="border: 1px solid #ccc;">  $R_{1.}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $2$</td>
        <td style="border: 1px solid #ccc;">  $N_{2}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc; background-color: rgba(0, 255, 0, 0.5);">  $R_{22}$</td>
        <td style="border: 1px solid #ccc; background-color: rgba(0, 255, 0, 0.5);">  $R_{23}$</td>
        <td style="border: 1px solid #ccc; background-color: rgba(0, 255, 0, 0.5);">  $R_{24}$</td>
		<td style="border: 1px solid #ccc;">  $R_{2.}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $3$</td>
        <td style="border: 1px solid #ccc;">  $N_{3}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $R_{33}$</td>
        <td style="border: 1px solid #ccc;">  $R_{34}$</td>
		<td style="border: 1px solid #ccc;">  $R_{3.}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $4$</td>
        <td style="border: 1px solid #ccc;">  $N_{4}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $R_{44}$</td>
		<td style="border: 1px solid #ccc;">  $R_{4.}$</td>
      </tr>
	  <tr>
		<td style="border: 1px solid #ccc;" colspan=2> $\text{Column totals}$</td>
		<td style="border: 1px solid #ccc;">  $R_{.1}$</td>
		<td style="border: 1px solid #ccc;">  $R_{.2}$</td>
		<td style="border: 1px solid #ccc;">  $R_{.3}$</td>
		<td style="border: 1px solid #ccc;">  $R_{.4}$</td>
		<td style="border: 1px solid #ccc;">  </td>
  	  </tr>
    </tbody>
  </table>
</div>

Let

$${::nomarkdown}
\begin{aligned}
Z_{i} &= T_{i} + U_{i} - Q_{i.} + V_{i} - P_{i.} \\
W_{i} &= R_{.i} + Q_{.i} - Q_{ii} + P_{.i} - P_{ii} - P_{i(i+1)} \\
C_{i} &= Q_{i.} + P_{i.} - P_{ii} \\
D_{i} &= Q_{ii} + P_{i(i+1)}
\end{aligned}
{:/}$$

Then maximising the likelihood yields the MLEs

$${::nomarkdown}
\begin{aligned}
\hat{S}_{i} &= \frac{R_{i.}}{N_{i}}\frac{N_{i+1}}{R_{(i+1).}}\frac{Z_{i} - W_{i}}{Z_{i}} \\
\hat{f}_{i} &= \frac{R_{i.}}{N_{i}}\frac{W_{i}}{Z_{i}} \\
\hat{S}'_{i} &= \frac{Q_{i.}}{M_{i}}\frac{N_{i+1}}{R_{(i+1).}}\frac{C_{i} - D_{i}}{C_{i}}  \\
\hat{f}'_{i} &= \frac{Q_{i.}}{M_{i}}\frac{D_{i}}{C_{i}} \\
\hat{S}''_{i} &= \frac{M_{i}}{L_{i}}\frac{P_{i.} - P_{ii}}{Q_{i.}} \\
\hat{f}''_{i} &= \frac{P_{ii}}{L_{i}}
\end{aligned}
{:/}$$

Recovery rate MLEs are given By

$${::nomarkdown}
\hat{r}^{\text{(age)}}_{i} = \frac{\hat{f}^{\text{(age)}}_{i}}{1-\hat{S}^{\text{(age)}}_{i}}
{:/}$$

So

$${::nomarkdown}
\begin{aligned}
\hat{r}_{i} &= \frac{R_{i.}W_{i}R_{i+1.}}{N_{i}R_{i+1.}Z_{i} - R_{i.}N_{i+1}(Z_{i} - W_{i})} \\
\hat{r}'_{i} &= \frac{Q_{i.}D_{i}R_{i+1.}}{M_{i}R_{i+1.}C_{i} - Q_{i.}N_{i+1}(C_{i} - D_{i})} \\
\hat{r}''_{i} &= \frac{P_{ii}Q_{i.}}{L_{i}Q_{i.} - M_{i}(P_{i.} - P_{ii})}
\end{aligned}
{:/}$$

### Variances

By the multivariate delta method, we obtain variances

$${::nomarkdown}
\begin{aligned}
\text{Var}\left(\hat{S}_{i}\right) &= \hat{S_{i}}^{2}\left(\frac{1}{R_{i.}} - \frac{1}{N_{i}} + \frac{1}{R_{i+1.}} - \frac{1}{N_{i+1}} + \frac{1}{Z_{i} - W_{i}} - \frac{1}{Z_{i}}\right)\\
\text{Var}\left(\hat{f}_{i}\right) &= \hat{f_{i}}^{2}\left(\frac{1}{R_{i.}} - \frac{1}{N_{i}} + \frac{1}{W_{i}} - \frac{1}{Z_{i}}\right) \\
\text{Var}\left(\hat{S}'_{i}\right) &= \hat{S_{i}}^{2}\left(\frac{1}{Q_{i.}} - \frac{1}{M_{i}} + \frac{1}{R_{i+1.}} - \frac{1}{N_{i+1}} + \frac{1}{C_{i} - D_{i}} - \frac{1}{C_{i}}\right) \\
\text{Var}\left(\hat{f}'_{i}\right) &= \hat{f_{i}}^{2}\left(\frac{1}{Q_{i.}} - \frac{1}{M_{i}} + \frac{1}{D_{i}} - \frac{1}{C_{i}}\right) \\
\text{Var}\left(\hat{S}''_{i}\right) &= \hat{S''_{i}}^{2}\left(\frac{1}{Q_{i.}} - \frac{1}{M_{i}} + \frac{1}{P_{i.} - P_{ii}} - \frac{1}{L_{i}}\right) \\
\text{Var}\left(\hat{f}''_{i}\right) &= \hat{f''_{i}}^{2}\left(\frac{1}{P_{ii}} - \frac{1}{L_{i}}\right) \\\\
\end{aligned}
{:/}$$

## Combined post-fledging recovery data

### The data

In the case where post-fledging recoveries are <i>not</i> recorded separately to juvenile recoveries for birds ringed as pulli, the pullus recovery data will be stored as in the table below

<div style="text-align: center; margin: 1em 0;">
  <table style="margin: 0 auto; border-collapse: collapse; width: auto;">
    <thead>
      <tr>
        <th colspan="6">Observed recovery counts of birds ringed as pulli</th>
      </tr>
      <tr>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Year marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Number marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" colspan="4">$\text{Year recovered}$</th>
      </tr>
      <tr>
	<th style="border: 1px solid #ccc; padding: 6px 12px;">$1$</th>
	<th style="border: 1px solid #ccc; padding: 6px 12px;">$2$</th>
	<th style="border: 1px solid #ccc; padding: 6px 12px;">$3$</th>
	<th style="border: 1px solid #ccc; padding: 6px 12px;">$4$</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td style="border: 1px solid #ccc;">  $1$</td>
        <td style="border: 1px solid #ccc;">  $L_{1}$</td>
        <td style="border: 1px solid #ccc;">  $P_{11}$</td>
        <td style="border: 1px solid #ccc;">  $P_{12}$</td>
        <td style="border: 1px solid #ccc;">  $P_{13}$</td>
        <td style="border: 1px solid #ccc;">  $P_{14}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $2$</td>
        <td style="border: 1px solid #ccc;">  $L_{2}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $P_{22}$</td>
        <td style="border: 1px solid #ccc;">  $P_{23}$</td>
        <td style="border: 1px solid #ccc;">  $P_{24}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $3$</td>
        <td style="border: 1px solid #ccc;">  $L_{3}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $P_{33}$</td>
        <td style="border: 1px solid #ccc;">  $P_{34}$</td>

      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $4$</td>
        <td style="border: 1px solid #ccc;">  $L_{4}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $P_{44}$</td>
      </tr>
    </tbody>
  </table>
</div> 

The first cell in each row is the number of birds from cohort $i$ recovered in their first year of life, irrespective of time of recovery.

### The model

Under the combined post-fledging model, adults and juveniles are modelled identically as above, but the expected counts of recoveries in the post-fledging period are given by the table below

<div style="text-align: center; margin: 1em 0;">
  <table style="margin: 0 auto; border-collapse: collapse; width: auto;">
    <thead>
      <tr>
        <th colspan="6">Expected recovery counts of birds ringed as pulli</th>
      </tr>
      <tr>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Year marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Number marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" colspan="4">$\text{Year recovered}$</th>
      </tr>
      <tr>
	<th style="border: 1px solid #ccc; padding: 6px 12px;">$1$</th>
	<th style="border: 1px solid #ccc; padding: 6px 12px;">$2$</th>
	<th style="border: 1px solid #ccc; padding: 6px 12px;">$3$</th>
	<th style="border: 1px solid #ccc; padding: 6px 12px;">$4$</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td style="border: 1px solid #ccc;">  $1$</td>
        <td style="border: 1px solid #ccc;">  $L_{1}$</td>
        <td style="border: 1px solid #ccc;">  $L_{1}(f_{1}'' + S_{1}'' f_{1}')$</td>
        <td style="border: 1px solid #ccc;">  $L_{1}S_{1}'' S_{1}' f_{2}$</td>
        <td style="border: 1px solid #ccc;">  $L_{1}S_{1}'' S_{1}' S_{2}f_{3}$</td>
        <td style="border: 1px solid #ccc;">  $L_{1}S_{1}'' S_{1}' S_{2}S_{3}f_{4}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $2$</td>
        <td style="border: 1px solid #ccc;">  $L_{2}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $L_{2}(f_{2}'' + S_{2}'' f_{2}')$</td>
        <td style="border: 1px solid #ccc;">  $L_{2}S_{2}'' S_{2}' f_{3}$</td>
        <td style="border: 1px solid #ccc;">  $L_{2}S_{2}'' S_{2}' S_{3} f_{4}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $3$</td>
        <td style="border: 1px solid #ccc;">  $L_{3}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $L_{3}(f_{3}'' + S_{3}'' f_{3}')$</td>
        <td style="border: 1px solid #ccc;">  $L_{3}S_{3}'' S_{3}' f_{4}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $4$</td>
        <td style="border: 1px solid #ccc;">  $L_{4}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $L_{4}(f_{4}'' + S_{4}'' f_{4}')$</td>
      </tr>
    </tbody>
  </table>
</div>

The explanation for this is that, given a bird has been ringed as a pullus, the probability of it being recovered in the first year is the joint probability of it dying and being recovered during the post-fledging period + the probability of it surviving the post-fledging stage and then dying and being recovered as a juvenile.

Details on the Seber parameterisation are below

<details>
<summary>Seber parameterisation</summary>

<div style="text-align: center; margin: 1em 0;">
  <table style="margin: 0 auto; border-collapse: collapse; width: auto;">
    <thead>
      <tr>
        <th colspan="6">Expected recovery counts of birds ringed as pulli</th>
      </tr>
      <tr>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Year marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Number marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" colspan="4">$\text{Year recovered}$</th>
      </tr>
      <tr>
	<th style="border: 1px solid #ccc; padding: 6px 12px;">$1$</th>
	<th style="border: 1px solid #ccc; padding: 6px 12px;">$2$</th>
	<th style="border: 1px solid #ccc; padding: 6px 12px;">$3$</th>
	<th style="border: 1px solid #ccc; padding: 6px 12px;">$4$</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td style="border: 1px solid #ccc;">  $1$</td>
        <td style="border: 1px solid #ccc;">  $L_{1}$</td>
        <td style="border: 1px solid #ccc;">  $L_{1}((1 - S_{1}'' )r_{1}'' + S_{1}'' (1 - S_{1}')r_{1}')$</td>
        <td style="border: 1px solid #ccc;">  $L_{1}S_{1}'' S_{1}' (1 - S_{2})r_{2}$</td>
        <td style="border: 1px solid #ccc;">  $L_{1}S_{1}'' S_{1}' S_{2} (1 - S_{3})r_{3}$</td>
        <td style="border: 1px solid #ccc;">  $L_{1}S_{1}'' S_{1}' S_{2}S_{3} (1 - S_{4})r_{4}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $2$</td>
        <td style="border: 1px solid #ccc;">  $L_{2}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $L_{2}((1 - S_{2}'' )r_{2}'' + S_{2}'' (1 - S_{2}')r_{2}')$</td>
        <td style="border: 1px solid #ccc;">  $L_{2}S_{2}'' S_{2}' (1 - S_{3})r_{3}$</td>
        <td style="border: 1px solid #ccc;">  $L_{2}S_{2}'' S_{2}' S_{3} (1 - S_{4})r_{4}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $3$</td>
        <td style="border: 1px solid #ccc;">  $L_{3}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $L_{3}((1 - S_{3}'' )r_{3}'' + S_{3}'' (1 - S_{3}')r_{3}')$</td>
        <td style="border: 1px solid #ccc;">  $L_{3}S_{3}'' S_{3}' (1 - S_{4})r_{4}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $4$</td>
        <td style="border: 1px solid #ccc;">  $L_{4}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $L_{4}((1 - S_{4}'' )r_{4}'' + S_{4}'' (1 - S_{4}')r_{4}')$</td>
      </tr>
    </tbody>
  </table>
</div>

As above, $S_{4}$, $r_{4}$, $S_{4}'$, and $r_{4}'$ are not identifiable. So the post-fledging table becomes

<div style="text-align: center; margin: 1em 0;">
  <table style="margin: 0 auto; border-collapse: collapse; width: auto;">
    <thead>
      <tr>
        <th colspan="6">Expected recovery counts of birds ringed as pulli</th>
      </tr>
      <tr>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Year marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Number marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" colspan="4">$\text{Year recovered}$</th>
      </tr>
      <tr>
	<th style="border: 1px solid #ccc; padding: 6px 12px;">$1$</th>
	<th style="border: 1px solid #ccc; padding: 6px 12px;">$2$</th>
	<th style="border: 1px solid #ccc; padding: 6px 12px;">$3$</th>
	<th style="border: 1px solid #ccc; padding: 6px 12px;">$4$</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td style="border: 1px solid #ccc;">  $1$</td>
        <td style="border: 1px solid #ccc;">  $L_{1}$</td>
        <td style="border: 1px solid #ccc;">  $L_{1}((1 - S_{1}'' )r_{1}'' + S_{1}'' (1 - S_{1}')r_{1}')$</td>
        <td style="border: 1px solid #ccc;">  $L_{1}S_{1}'' S_{1}' (1 - S_{2})r_{2}$</td>
        <td style="border: 1px solid #ccc;">  $L_{1}S_{1}'' S_{1}' S_{2} (1 - S_{3})r_{3}$</td>
        <td style="border: 1px solid #ccc;">  $L_{1}S_{1}'' S_{1}' S_{2}S_{3} f_{4}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $2$</td>
        <td style="border: 1px solid #ccc;">  $L_{2}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $L_{2}((1 - S_{2}'' )r_{2}'' + S_{2}'' (1 - S_{2}')r_{2}')$</td>
        <td style="border: 1px solid #ccc;">  $L_{2}S_{2}'' S_{2}' (1 - S_{3})r_{3}$</td>
        <td style="border: 1px solid #ccc;">  $L_{2}S_{2}'' S_{2}' S_{3} f_{4}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $3$</td>
        <td style="border: 1px solid #ccc;">  $L_{3}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $L_{3}((1 - S_{3}'' )r_{3}'' + S_{3}'' (1 - S_{3}')r_{3}')$</td>
        <td style="border: 1px solid #ccc;">  $L_{3}S_{3}'' S_{3}' f_{4}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $4$</td>
        <td style="border: 1px solid #ccc;">  $L_{4}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $L_{4}((1 - S_{4}'' )r_{4}'' + S_{4}'' f_{4}')$</td>
      </tr>
    </tbody>
  </table>
</div>

</details>

### The likelihood

This model gives the row-wise likelihoods

$${::nomarkdown}
\begin{aligned}
\ln\left(\mathcal{L}_{p}\right) &\propto R_{pp}\ln(f_{p}) + R_{p(p+1)}\ln(S_{p}f_{p+1}) + \dots + R_{pi}\ln(S_{p}S_{p+1}\dots S_{i-1}f_{i}) + \\ &\quad R_{p(i+1)}\ln(S_{p}S_{p+1}\dots S_{i-1}S_{i}f_{i+1}) + \dots + R_{pl}\ln(S_{p}S_{p+1}\dots S_{l-1}f_{l}) + (N_{p} - R_{p.})\ln(1 - \theta_{p}) \\
\ln\left(\mathcal{L}'_{p}\right) &\propto Q_{pp}\ln(f'_{p}) + Q_{p(p+1)}\ln(S'_{p}f_{p+1}) + \dots + Q_{pi}\ln(S'_{p}S_{p+1}\dots S_{i-1}f_{i}) + \\ &\quad Q_{p(i+1)}\ln(S'_{p}S_{p+1}\dots S_{i-1}S_{i}f_{i+1}) + \dots + Q_{pl}\ln(S'_{p}S_{p+1}\dots S_{l-1}f_{l}) + (M_{p} - Q_{p.})\ln(1 - \theta'_{p}) \\
\ln\left(\mathcal{L}''_{p}\right) &\propto P_{pp}\ln(f''_{p} + S''_{p}f'_{p}) + P_{p(p+1)}\ln(S''_{p}S'_{p}f_{p+1}) + \dots + P_{pi}\ln(S''_{p}S'_{p}S_{p+1}\dots S_{i-1}f_{i}) + \\ &\quad P_{p(i+1)}\ln(S''_{p}S'_{p}S_{p+1}\dots S_{i-1}S_{i}f_{i+1}) + \dots + P_{pl}\ln(S''_{p}S'_{p}S_{p+1}\dots S_{l-1}f_{l}) + (L_{p} - P_{p.})\ln(1 - \theta''_{p})
\end{aligned}
{:/}$$

where $\theta_{i}$, $\theta_{i}'$, and $\theta_{i}''$ are the same as above. The total likelihood is given by summing these likelihoods over all rows, then adding them.

### MLEs

Let

$${::nomarkdown}
\begin{aligned}
Z_{i} &= T_{i} + U_{i} - Q_{i.} + V_{i} - P_{i.} \\
W_{i} &= R_{.i} + Q_{.i} - Q_{ii} + P_{.i} - P_{ii}
\end{aligned}
{:/}$$

Then maximising the likelihood yields

$${::nomarkdown}
\begin{aligned}
\hat{S}_{i}   &= \frac{R_{i.}N_{i+1}}{N_{i}R_{i+1.}}\frac{Z_{i} - W_{i}}{Z_{i}} \\
\hat{f}_{i}   &= \frac{R_{i.}}{N_{i}}\frac{W_{i}}{Z_{i}} \\
\hat{S}'_{i}  &= \frac{N_{i+1}}{R_{i+1.}}\frac{Q_{i.} - Q_{ii}}{M_{i}} \\
\hat{f}'_{i}  &= \frac{Q_{ii}}{M_{i}} \\
\hat{S}''_{i} &= \frac{M_{i}}{L_{i}} \frac{P_{i.} - P_{ii}}{Q_{i.} - Q_{ii}} \\
\hat{f}''_{i} &= \frac{P_{ii}Q_{i.} - P_{i.}Q_{ii}}{L_{i}(Q_{i.} - Q_{ii})} \\
\end{aligned}
{:/}$$

And

$${::nomarkdown}
\begin{aligned}
\hat{r}_{i} &= \frac{R_{i.}W_{i}R_{i+1.}}{N_{i}R_{i+1.}Z_{i} - R_{i.}N_{i+1}(Z_{i} - W_{i})} \\
\hat{r}'_{i} &= \frac{Q_{ii}R_{i+1.}}{M_{i}R_{i+1.} - N_{i+1}(Q_{i.} - Q_{ii})} \\
\hat{r}''_{i} &= \frac{P_{ii}Q_{i.} - P_{i.}Q_{ii}}{L_{i}(Q_{i.} - Q_{ii}) - M_{i}(P_{i.} - P_{ii})}
\end{aligned}
{:/}$$

### Variances

By the multivariate delta method, we obtain variances

$${::nomarkdown}
\begin{aligned}
\text{Var}(\hat{f}''_{i}) &= \hat{f''_{i}}^{2}\left(\frac{1}{Q_{i.} - Q_{ii}} - \frac{1}{L_{i}} + \frac{Q_{i.}P_{ii}(Q_{i.} - P_{ii}) + P_{i.}Q_{ii}(Q_{ii} + P_{i.}) - 2Q_{ii}P_{ii}Q_{i.}}{(P_{ii}Q_{i.} - P_{i.}Q_{ii})^{2}}\right) \\
\text{Var}(\hat{S}''_{i}) &= \hat{S''_{i}}^{2}\left(\frac{1}{Q_{i.} - Q_{ii}} -\frac{1}{M_{i}} + \frac{1}{P_{i.} - P_{ii}} - \frac{1}{L_{i}}\right)\\
\text{Var}(\hat{f}'_{i})  &= \hat{f'_{i}}^{2}\left(\frac{1}{Q_{ii}} - \frac{1}{M_{i}}\right) \\
\text{Var}(\hat{S}'_{i})  &= \hat{S'_{i}}^{2}\left(\frac{1}{R_{i+1.}} - \frac{1}{N_{i+1}} + \frac{1}{Q_{i.} - Q_{ii}} - \frac{1}{M_{i}}\right) \\
\text{Var}(\hat{f}_{i})   &= \hat{f_{i}}^{2}\left(\frac{1}{R_{i.}} - \frac{1}{N_{i}} + \frac{1}{W_{i}} - \frac{1}{Z_{i}}\right) \\
\text{Var}(\hat{S}_{i})   &= \hat{S_{i}}^{2}\left(\frac{1}{R_{i.}} - \frac{1}{N_{i}} + \frac{1}{R_{i+1.}} - \frac{1}{N_{i+1}} + \frac{1}{Z_{i} - W_{i}} - \frac{1}{Z_{i}}\right)
\end{aligned}
{:/}$$

As previously stated, there is no benefit to using this model, as the date of recovery is always recorded, meaning we are never in the scenario where we only have access to combined post-fledging and juvenile recoveries. It is presented to demonstrate my ability to research current models, develop them, and criticise them.

## Goodness of fit

To assess goodness of fit, we can construct expected recovery tables for the relevant model, and then compare this to 

## An example

### The data

The blackbird data collected from 1964-1983 used in Robinson(2012) is shown below

<style>
table td {
  border: 1px solid #ccc;
}
table th {
  border: 1px solid #ccc; padding: 6px 12px;
}
</style>

<details>
<summary>Birds ringed as adults</summary>
<div style="text-align: center; margin: 1em 0;">
<table style="margin: 0 auto; border-collapse: collapse; width: auto;">
  <thead>
    <tr>
      <th colspan="22">Birds ringed as adults</th>
    </tr>
    <tr>
      <th rowspan="2">Year of ringing</th>
      <th colspan="20">Year recovered</th>
      <th rowspan="2">Total ringed</th>
    </tr>
    <tr>
      <th>1</th><th>2</th><th>3</th><th>4</th><th>5</th>
      <th>6</th><th>7</th><th>8</th><th>9</th><th>10</th>
      <th>11</th><th>12</th><th>13</th><th>14</th><th>15</th>
      <th>16</th><th>17</th><th>18</th><th>19</th><th>20</th>
    </tr>
  </thead>
  <tbody>
    <tr><td>1964</td><td>39</td><td>18</td><td>19</td><td>8</td><td>9</td><td>3</td><td>0</td><td>2</td><td>5</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>1891</td></tr>
<tr><td>1965</td><td>0</td><td>44</td><td>23</td><td>16</td><td>15</td><td>16</td><td>4</td><td>6</td><td>1</td><td>1</td><td>0</td><td>0</td><td>1</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>2344</td></tr>
<tr><td>1966</td><td>0</td><td>0</td><td>32</td><td>27</td><td>10</td><td>11</td><td>10</td><td>6</td><td>3</td><td>4</td><td>1</td><td>1</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>2354</td></tr>
<tr><td>1967</td><td>0</td><td>0</td><td>0</td><td>42</td><td>29</td><td>24</td><td>12</td><td>10</td><td>6</td><td>4</td><td>4</td><td>4</td><td>3</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>2993</td></tr>
<tr><td>1968</td><td>0</td><td>0</td><td>0</td><td>0</td><td>38</td><td>31</td><td>15</td><td>14</td><td>11</td><td>4</td><td>6</td><td>5</td><td>1</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>2866</td></tr>
<tr><td>1969</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>62</td><td>29</td><td>18</td><td>20</td><td>15</td><td>12</td><td>8</td><td>4</td><td>2</td><td>2</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>2897</td></tr>
<tr><td>1970</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>47</td><td>25</td><td>21</td><td>20</td><td>18</td><td>7</td><td>3</td><td>3</td><td>0</td><td>2</td><td>0</td><td>0</td><td>0</td><td>0</td><td>3056</td></tr>
<tr><td>1971</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>32</td><td>39</td><td>20</td><td>15</td><td>11</td><td>13</td><td>3</td><td>2</td><td>4</td><td>0</td><td>0</td><td>0</td><td>0</td><td>3311</td></tr>
<tr><td>1972</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>38</td><td>28</td><td>25</td><td>20</td><td>14</td><td>9</td><td>3</td><td>3</td><td>0</td><td>0</td><td>1</td><td>0</td><td>3643</td></tr>
<tr><td>1973</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>42</td><td>33</td><td>24</td><td>17</td><td>10</td><td>7</td><td>5</td><td>1</td><td>1</td><td>0</td><td>0</td><td>3763</td></tr>
<tr><td>1974</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>35</td><td>28</td><td>26</td><td>16</td><td>11</td><td>3</td><td>6</td><td>2</td><td>0</td><td>0</td><td>3668</td></tr>
<tr><td>1975</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>37</td><td>25</td><td>15</td><td>10</td><td>9</td><td>10</td><td>4</td><td>4</td><td>0</td><td>4931</td></tr>
<tr><td>1976</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>36</td><td>31</td><td>19</td><td>12</td><td>8</td><td>6</td><td>1</td><td>2</td><td>4317</td></tr>
<tr><td>1977</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>41</td><td>23</td><td>17</td><td>15</td><td>11</td><td>2</td><td>2</td><td>4070</td></tr>
<tr><td>1978</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>38</td><td>16</td><td>18</td><td>13</td><td>10</td><td>8</td><td>4053</td></tr>
<tr><td>1979</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>41</td><td>31</td><td>28</td><td>7</td><td>9</td><td>3936</td></tr>
<tr><td>1980</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>50</td><td>38</td><td>23</td><td>16</td><td>4505</td></tr>
<tr><td>1981</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>48</td><td>26</td><td>30</td><td>4820</td></tr>
<tr><td>1982</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>25</td><td>21</td><td>4080</td></tr>
<tr><td>1983</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>41</td><td>4629</td></tr>
  </tbody>
</table>
</div>
</details>

<details>
<summary>Birds ringed as juveniles</summary>
<div style="text-align: center; margin: 1em 0;">
<table style="margin: 0 auto; border-collapse: collapse; width: auto;">
  <thead>
    <tr>
      <th colspan="22">Birds ringed as juveniles</th>
    </tr>
    <tr>
      <th rowspan="2">Year of ringing</th>
      <th colspan="20">Year recovered</th>
      <th rowspan="2">Total ringed</th>
    </tr>
    <tr>
      <th>1</th><th>2</th><th>3</th><th>4</th><th>5</th>
      <th>6</th><th>7</th><th>8</th><th>9</th><th>10</th>
      <th>11</th><th>12</th><th>13</th><th>14</th><th>15</th>
      <th>16</th><th>17</th><th>18</th><th>19</th><th>20</th>
    </tr>
  </thead>
  <tbody>
    <tr><td>1964</td><td>114</td><td>39</td><td>17</td><td>12</td><td>10</td><td>12</td><td>8</td><td>5</td><td>3</td><td>3</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>4185</td></tr>
<tr><td>1965</td><td>0</td><td>93</td><td>26</td><td>31</td><td>19</td><td>16</td><td>6</td><td>7</td><td>3</td><td>6</td><td>1</td><td>1</td><td>0</td><td>0</td><td>0</td><td>1</td><td>0</td><td>0</td><td>0</td><td>0</td><td>4411</td></tr>
<tr><td>1966</td><td>0</td><td>0</td><td>70</td><td>32</td><td>19</td><td>20</td><td>8</td><td>5</td><td>4</td><td>4</td><td>2</td><td>1</td><td>1</td><td>1</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>3954</td></tr>
<tr><td>1967</td><td>0</td><td>0</td><td>0</td><td>85</td><td>34</td><td>21</td><td>25</td><td>13</td><td>11</td><td>4</td><td>2</td><td>4</td><td>3</td><td>0</td><td>2</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>4865</td></tr>
<tr><td>1968</td><td>0</td><td>0</td><td>0</td><td>0</td><td>75</td><td>30</td><td>34</td><td>18</td><td>16</td><td>8</td><td>11</td><td>5</td><td>6</td><td>2</td><td>1</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>5232</td></tr>
<tr><td>1969</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>76</td><td>30</td><td>28</td><td>25</td><td>20</td><td>9</td><td>7</td><td>5</td><td>5</td><td>2</td><td>2</td><td>0</td><td>0</td><td>0</td><td>0</td><td>4918</td></tr>
<tr><td>1970</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>61</td><td>27</td><td>23</td><td>21</td><td>13</td><td>6</td><td>7</td><td>4</td><td>3</td><td>2</td><td>0</td><td>0</td><td>0</td><td>0</td><td>4114</td></tr>
<tr><td>1971</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>53</td><td>34</td><td>15</td><td>14</td><td>11</td><td>10</td><td>4</td><td>8</td><td>1</td><td>1</td><td>0</td><td>0</td><td>0</td><td>3715</td></tr>
<tr><td>1972</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>74</td><td>21</td><td>17</td><td>14</td><td>11</td><td>8</td><td>9</td><td>6</td><td>3</td><td>1</td><td>1</td><td>0</td><td>3943</td></tr>
<tr><td>1973</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>81</td><td>28</td><td>25</td><td>19</td><td>14</td><td>13</td><td>9</td><td>6</td><td>5</td><td>2</td><td>2</td><td>4071</td></tr>
<tr><td>1974</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>67</td><td>16</td><td>11</td><td>11</td><td>11</td><td>10</td><td>2</td><td>1</td><td>0</td><td>0</td><td>3300</td></tr>
<tr><td>1975</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>71</td><td>33</td><td>17</td><td>15</td><td>7</td><td>6</td><td>1</td><td>5</td><td>0</td><td>4020</td></tr>
<tr><td>1976</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>68</td><td>28</td><td>17</td><td>11</td><td>13</td><td>5</td><td>8</td><td>4</td><td>4498</td></tr>
<tr><td>1977</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>82</td><td>34</td><td>23</td><td>13</td><td>9</td><td>7</td><td>3</td><td>5031</td></tr>
<tr><td>1978</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>74</td><td>30</td><td>13</td><td>15</td><td>8</td><td>7</td><td>4615</td></tr>
<tr><td>1979</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>75</td><td>29</td><td>17</td><td>17</td><td>13</td><td>4997</td></tr>
<tr><td>1980</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>61</td><td>35</td><td>24</td><td>13</td><td>5536</td></tr>
<tr><td>1981</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>62</td><td>31</td><td>22</td><td>5417</td></tr>
<tr><td>1982</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>59</td><td>36</td><td>5471</td></tr>
<tr><td>1983</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>99</td><td>6843</td></tr>
  </tbody>
</table>
</div>
</details>

<details>
<summary>Birds ringed as pulli(sep)</summary>
<div style="text-align: center; margin: 1em 0;">
<table style="margin: 0 auto; border-collapse: collapse; width: auto;">
  <thead>
    <tr>
      <th colspan="23">Birds ringed as pulli(sep)</th>
    </tr>
    <tr>
      <th rowspan="2">Year of ringing</th>
      <th colspan="21">Year recovered</th>
      <th rowspan="2">Total ringed</th>
    </tr>
    <tr>
      <th>PF</th><th>Juv.</th><th>2</th><th>3</th><th>4</th><th>5</th>
      <th>6</th><th>7</th><th>8</th><th>9</th><th>10</th>
      <th>11</th><th>12</th><th>13</th><th>14</th><th>15</th>
      <th>16</th><th>17</th><th>18</th><th>19</th><th>20</th>
    </tr>
  </thead>
  <tbody>
    <tr><td>1964</td><td>52</td><td>15</td><td>10</td><td>2</td><td>8</td><td>5</td><td>3</td><td>1</td><td>0</td><td>1</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>2391</td></tr>
<tr><td>1965</td><td>0</td><td>74</td><td>30</td><td>14</td><td>18</td><td>7</td><td>12</td><td>2</td><td>1</td><td>3</td><td>1</td><td>1</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>3420</td></tr>
<tr><td>1966</td><td>0</td><td>0</td><td>78</td><td>29</td><td>20</td><td>11</td><td>4</td><td>4</td><td>2</td><td>0</td><td>2</td><td>1</td><td>0</td><td>1</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>4366</td></tr>
<tr><td>1967</td><td>0</td><td>0</td><td>0</td><td>67</td><td>22</td><td>12</td><td>4</td><td>7</td><td>4</td><td>2</td><td>2</td><td>1</td><td>4</td><td>1</td><td>2</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>4187</td></tr>
<tr><td>1968</td><td>0</td><td>0</td><td>0</td><td>0</td><td>101</td><td>30</td><td>10</td><td>8</td><td>8</td><td>6</td><td>3</td><td>6</td><td>1</td><td>1</td><td>1</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>4172</td></tr>
<tr><td>1969</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>81</td><td>27</td><td>10</td><td>11</td><td>10</td><td>4</td><td>1</td><td>3</td><td>1</td><td>3</td><td>1</td><td>0</td><td>0</td><td>1</td><td>0</td><td>0</td><td>4364</td></tr>
<tr><td>1970</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>58</td><td>15</td><td>8</td><td>5</td><td>4</td><td>4</td><td>3</td><td>2</td><td>0</td><td>0</td><td>1</td><td>1</td><td>0</td><td>0</td><td>0</td><td>3347</td></tr>
<tr><td>1971</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>55</td><td>19</td><td>11</td><td>10</td><td>6</td><td>6</td><td>5</td><td>3</td><td>0</td><td>1</td><td>1</td><td>0</td><td>0</td><td>0</td><td>3344</td></tr>
<tr><td>1972</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>67</td><td>21</td><td>14</td><td>6</td><td>7</td><td>6</td><td>1</td><td>0</td><td>2</td><td>2</td><td>0</td><td>1</td><td>0</td><td>3618</td></tr>
<tr><td>1973</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>54</td><td>26</td><td>6</td><td>8</td><td>2</td><td>4</td><td>2</td><td>1</td><td>0</td><td>1</td><td>1</td><td>1</td><td>3033</td></tr>
<tr><td>1974</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>42</td><td>17</td><td>5</td><td>9</td><td>3</td><td>1</td><td>1</td><td>3</td><td>0</td><td>0</td><td>1</td><td>2729</td></tr>
<tr><td>1975</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>57</td><td>12</td><td>8</td><td>4</td><td>2</td><td>5</td><td>0</td><td>0</td><td>1</td><td>0</td><td>3077</td></tr>
<tr><td>1976</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>58</td><td>15</td><td>10</td><td>5</td><td>8</td><td>2</td><td>3</td><td>2</td><td>0</td><td>3038</td></tr>
<tr><td>1977</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>64</td><td>13</td><td>7</td><td>7</td><td>4</td><td>3</td><td>1</td><td>1</td><td>3435</td></tr>
<tr><td>1978</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>60</td><td>21</td><td>8</td><td>10</td><td>9</td><td>1</td><td>4</td><td>3533</td></tr>
<tr><td>1979</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>62</td><td>15</td><td>11</td><td>4</td><td>9</td><td>3</td><td>3814</td></tr>
<tr><td>1980</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>54</td><td>15</td><td>8</td><td>9</td><td>3</td><td>3314</td></tr>
<tr><td>1981</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>57</td><td>18</td><td>8</td><td>5</td><td>3422</td></tr>
<tr><td>1982</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>41</td><td>14</td><td>14</td><td>2858</td></tr>
<tr><td>1983</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>70</td><td>24</td><td>4056</td></tr>
  </tbody>
</table>
</div>
</details>

<details>
<summary>Birds ringed as pulli(comb)</summary>
<div style="text-align: center; margin: 1em 0;">
<table style="margin: 0 auto; border-collapse: collapse; width: auto;">
  <thead>
    <tr>
      <th colspan="22">Birds ringed as pulli(comb)</th>
    </tr>
    <tr>
      <th rowspan="2">Year of ringing</th>
      <th colspan="20">Year recovered</th>
      <th rowspan="2">Total ringed</th>
    </tr>
    <tr>
      <th>1</th><th>2</th><th>3</th><th>4</th><th>5</th>
      <th>6</th><th>7</th><th>8</th><th>9</th><th>10</th>
      <th>11</th><th>12</th><th>13</th><th>14</th><th>15</th>
      <th>16</th><th>17</th><th>18</th><th>19</th><th>20</th>
    </tr>
  </thead>
  <tbody>
    <tr><td>1964</td><td>67</td><td>10</td><td>2</td><td>8</td><td>5</td><td>3</td><td>1</td><td>0</td><td>1</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>2391</td></tr>
<tr><td>1965</td><td>0</td><td>104</td><td>14</td><td>18</td><td>7</td><td>12</td><td>2</td><td>1</td><td>3</td><td>1</td><td>1</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>3420</td></tr>
<tr><td>1966</td><td>0</td><td>0</td><td>107</td><td>20</td><td>11</td><td>4</td><td>4</td><td>2</td><td>0</td><td>2</td><td>1</td><td>0</td><td>1</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>4366</td></tr>
<tr><td>1967</td><td>0</td><td>0</td><td>0</td><td>89</td><td>12</td><td>4</td><td>7</td><td>4</td><td>2</td><td>2</td><td>1</td><td>4</td><td>1</td><td>2</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>4187</td></tr>
<tr><td>1968</td><td>0</td><td>0</td><td>0</td><td>0</td><td>131</td><td>10</td><td>8</td><td>8</td><td>6</td><td>3</td><td>6</td><td>1</td><td>1</td><td>1</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>4172</td></tr>
<tr><td>1969</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>108</td><td>10</td><td>11</td><td>10</td><td>4</td><td>1</td><td>3</td><td>1</td><td>3</td><td>1</td><td>0</td><td>0</td><td>1</td><td>0</td><td>0</td><td>4364</td></tr>
<tr><td>1970</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>73</td><td>8</td><td>5</td><td>4</td><td>4</td><td>3</td><td>2</td><td>0</td><td>0</td><td>1</td><td>1</td><td>0</td><td>0</td><td>0</td><td>3347</td></tr>
<tr><td>1971</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>74</td><td>11</td><td>10</td><td>6</td><td>6</td><td>5</td><td>3</td><td>0</td><td>1</td><td>1</td><td>0</td><td>0</td><td>0</td><td>3344</td></tr>
<tr><td>1972</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>88</td><td>14</td><td>6</td><td>7</td><td>6</td><td>1</td><td>0</td><td>2</td><td>2</td><td>0</td><td>1</td><td>0</td><td>3618</td></tr>
<tr><td>1973</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>80</td><td>6</td><td>8</td><td>2</td><td>4</td><td>2</td><td>1</td><td>0</td><td>1</td><td>1</td><td>1</td><td>3033</td></tr>
<tr><td>1974</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>59</td><td>5</td><td>9</td><td>3</td><td>1</td><td>1</td><td>3</td><td>0</td><td>0</td><td>1</td><td>2729</td></tr>
<tr><td>1975</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>69</td><td>8</td><td>4</td><td>2</td><td>5</td><td>0</td><td>0</td><td>1</td><td>0</td><td>3077</td></tr>
<tr><td>1976</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>73</td><td>10</td><td>5</td><td>8</td><td>2</td><td>3</td><td>2</td><td>0</td><td>3038</td></tr>
<tr><td>1977</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>77</td><td>7</td><td>7</td><td>4</td><td>3</td><td>1</td><td>1</td><td>3435</td></tr>
<tr><td>1978</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>81</td><td>8</td><td>10</td><td>9</td><td>1</td><td>4</td><td>3533</td></tr>
<tr><td>1979</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>77</td><td>11</td><td>4</td><td>9</td><td>3</td><td>3814</td></tr>
<tr><td>1980</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>69</td><td>8</td><td>9</td><td>3</td><td>3314</td></tr>
<tr><td>1981</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>75</td><td>8</td><td>5</td><td>3422</td></tr>
<tr><td>1982</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>55</td><td>14</td><td>2858</td></tr>
<tr><td>1983</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>94</td><td>4056</td></tr>
  </tbody>
</table>
</div>
</details>

### GLM estimates

While MLEs can be used, it is preferable to use a GLM, as it allows for the inclusion of covariates and ensures parameters are bounded (with the right link function). I use a logit link.

The following plots show the estimates and 95% confidence intervals for both $M_{\text{sep}}$ and $M_{\text{comb}}$ models from the Brownie GLM.

<details>
<summary>Brownie GLM plots</summary>

<div style="text-align: center;">
  <div class = "row">
      <img src="/assets/images/f0_Brownie_GLM_comparison.png" width="400">
      <img src="/assets/images/S0_Brownie_GLM_comparison.png" width="400">
  </div>
</div>

As expected, the models produce near identical estimates for adults. We see a general decline in reported exploitation rates for adult blackbirds over time and a fluctuating survival rate. Survival rates fluctuate around 70%, with high uncertainty for estimates near 1, i.e. in year 11 and 18.

<!--  By the definition of $f$, this suggests that the reporting rate decreased over the time of the study, reflecting trends in reporting behaviour (<a>https://www.researchgate.net/publication/254310554_Declining_rates_of_ring_recovery_in_British_birds</a>).  -->

<div style="text-align: center;">
  <div class = "row">
      <img src="/assets/images/f1_Brownie_GLM_comparison.png" width="400">
      <img src="/assets/images/S1_Brownie_GLM_comparison.png" width="400">
  </div>
</div>

Similarly for juveniles, the values are very similar between models. We see a decline in reported exploitation rates of juveniles up until the middle of the survey, where it increases. Survival rates appear to fluctuate over the duration of the study, but increase from around 50% to 75%.

<div style="text-align: center;">
  <div class = "row">
      <img src="/assets/images/f2_Brownie_GLM_comparison_exLast.png" width="400">
      <img src="/assets/images/S2_Brownie_GLM_comparison.png" width="400">
  </div>
</div>

Post-fledging estimates exhibit the greatest difference between models. As expected, the more informative data produces estimates with less uncertainty. Reported exploitation rates show a more gradual decline. Survival rate stay fairly constant around 45%, with a possible increase towards the end of the study; there is more uncertainty for these estimates however. The final $M_{\text{comb}}$ estimate for $f''$ was removed due to uncertainty affecting readability. The plot is below

<div style="text-align: center;">
  <img src="/assets/images/f2_Brownie_GLM_comparison.png" width="400">
</div>

</details>

Below are the estimates from the Seber GLM

<details>
<summary>Seber GLM plots</summary>

<div style="text-align: center;">
  <div class = "row">
      <img src="/assets/images/r0_Seber_GLM_comparison.png" width="400">
      <img src="/assets/images/S0_Seber_GLM_comparison.png" width="400">
  </div>
</div>

Here, we see that the previously observed uncertainty in high survival rate estimates is lower, while the uncertainty of recovery rate estimates is much higher in these years. The recovery rates of adults fluctuate with no clear trend. Both models produce identical $r$ estimates, as expected.

<div style="text-align: center;">
  <div class = "row">
      <img src="/assets/images/r1_Seber_GLM_comparison.png" width="400">
      <img src="/assets/images/S1_Seber_GLM_comparison.png" width="400">
  </div>
</div>

Recovery rates appear to be much higher in the 10th year, after which they decline. We see similar levels of uncertainty in $S'$ estimates. Between models, estimates are very similar.

<div style="text-align: center;">
  <div class = "row">
      <img src="/assets/images/r2_Seber_GLM_comparison_exLast.png" width="400">
      <img src="/assets/images/S2_Seber_GLM_comparison.png" width="400">
  </div>
</div>

As with the Brownie parameterisation, the difference between models is greatest here. Recovery rates show no clear trend. Final estimates are highly uncertain. The final $M_{\text{comb}}$ estimate for $r''$ has been removed for readability. The plot is below

<div style="text-align: center;">
  <img src="/assets/images/r2_Seber_GLM_comparison.png" width="400">
</div>

</details>

### Bayesian modelling

While estimating parameters using a GLM is standard and is computationally cheap, it does not allow us to incorporate any prior beliefs about the system we are modelling. Additionally, priors allow us to estimate the final $S$ and $r$ parameters, despite them being more influenced by the priors than other parameters. For this section, I will only consider the model with separate counts for the post-fledging period, as it is more informative. I present results from 2 random-walk models. These models use the same underlying multinomial framework, except they estimate $S$ and $r$, from which $f$ is estimated.

#### RW1

The random walk of order 1 enforces first order smoothing between parameters. I.e., the difference between parameters is assumed to be near 0. 

The priors for the variation in the random walk are given by

$${::nomarkdown}
\begin{aligned}
\sigma_{S^{\text{(age)}}} &\sim \text{N}^{+}\left(0, 1\right) \\
\sigma_{r^{\text{(age)}}} &\sim \text{N}^{+}\left(0, 1\right)
\end{aligned}
{:/}$$

The priors for the first parameter are given by the positive half-normal distribution
		
$${::nomarkdown}
\begin{aligned}
\text{logit}\left(S^{\text{(age)}}_{1}\right) &\sim \text{N}(0, 1) \\
\text{logit}\left(r^{\text{(age)}}_{1}\right) &\sim \text{N}(0, 1)
\end{aligned}
{:/}$$

The priors for survival and recovery rate parameters when $t > 1$ are given by

$${::nomarkdown}
\begin{aligned}
\text{logit}\left(S^{\text{(age)}}_{t}\right) - \text{logit}\left(S^{\text{(age)}}_{t-1}\right) &\sim \text{N}\left(0, \sigma_{S^{\text{(age)}}}^{2}\right) \\
\text{logit}\left(r^{\text{(age)}}_{t}\right) - \text{logit}\left(r^{\text{(age)}}_{t-1}\right) &\sim \text{N}\left(0, \sigma_{r^{\text{(age)}}}^{2}\right)
\end{aligned}
{:/}$$

These can be written as

$${::nomarkdown}
\begin{aligned}
\text{logit}\left(S^{\text{(age)}}_{t}\right) &\sim \text{N}\left(\text{logit}\left(S^{\text{(age)}}_{t-1}\right), \sigma_{S^{\text{(age)}}}^{2}\right) \\
\text{logit}\left(r^{\text{(age)}}_{t}\right) &\sim \text{N}\left(\text{logit}\left(r^{\text{(age)}}_{t-1}\right), \sigma_{r^{\text{(age)}}}^{2}\right)
\end{aligned}
{:/}$$

Fitting this model yields the following estimates for $f$, $r$, and $S$

<div style="text-align: center;">
  <div class = "row">
      <img src="/assets/images/f0_RW1.png" width="400">
      <img src="/assets/images/r0_RW1.png" width="400">
      <img src="/assets/images/S0_RW1.png" width="400">
  </div>
</div>

<!--
The estimates imply that, under the assumption that first differences follow a $N\left(0, \sigma_{S}^{2}\right)$ distribution, the adult recovery rate (and harvest rate) declined over the period of the scheme. We also see that the adult survival rate increased over the first 10 years from approximately 70% to 75%, after which it declined to 67%, then returned to 70%. This could suggest that the survival rate of blackbirds oscillate, potentially due to multi-year climate patterns. This does, however, rely on the assumption that the priors are realistic. 

For juveniles, we find
-->

<div style="text-align: center;">
  <div class = "row">
      <img src="/assets/images/f1_RW1.png" width="400">
      <img src="/assets/images/r1_RW1.png" width="400">
      <img src="/assets/images/S1_RW1.png" width="400">
  </div>
</div>

<!--
Unlike adults, we do not see a steady decline in recovery rates; they begin to decline from 5.5% in the first year to 4% in the 5th year, but then increase back up to 5.5% in the 10th year. After this, there is a decline to around 4%. This model suggests the survival rate of juveniles steadily increase from 55% to 70%. This is also unlike the adults. 

A possible explanation for this is that the natural predation on blackbirds decreased. For example, if species $A$ predated on blackbirds, and typically targeted the young as they were weak, but experienced an event causing their population to decline, then the juvenile survival rate might increase.

Finally, the rates for birds recovered during the post-fledging period are
-->

<div style="text-align: center;">
  <div class = "row">
      <img src="/assets/images/f2_RW1.png" width="400">
      <img src="/assets/images/r2_RW1.png" width="400">
      <img src="/assets/images/S2_RW1.png" width="400">
  </div>
</div>

The estimates of $\sigma$ are

<table style="margin: 0 auto; border-collapse: collapse; width: auto;">
<tr><th>Param</th><th>Age</th><th>Mean</th><th>Median</th><th>StdErr</th><th>Lower</th><th>Upper</th></tr>
<tr><td>r</td><td>a</td><td>0.10370696</td><td>0.09876766</td><td>0.0322859</td><td>0.05569357</td><td>0.180291</td></tr>
<tr><td>r</td><td>j</td><td>0.13022762</td><td>0.12635774</td><td>0.04364288</td><td>0.054742763</td><td>0.2268793</td></tr>
<tr><td>r</td><td>p</td><td>0.04811699</td><td>0.04159333</td><td>0.0328937</td><td>0.00331044</td><td>0.129522</td></tr>
<tr><td>s</td><td>a</td><td>0.10216709</td><td>0.09695040</td><td>0.04225906</td><td>0.034732977</td><td>0.1987097</td></tr>
<tr><td>s</td><td>j</td><td>0.11131672</td><td>0.10592293</td><td>0.04838992</td><td>0.030027302</td><td>0.2218385</td></tr>
<tr><td>s</td><td>p</td><td>0.05775504</td><td>0.04610405</td><td>0.04757393</td><td>0.002333356</td><td>0.1774066</td></tr>
</table>

#### RW2

In a random walk of order 2, we assume the second differences (difference of differences) is near to 0.

The priors for the variation in the random walk are given by the positive half-normal distribution

$${::nomarkdown}
\begin{aligned}
\sigma_{S^{\text{(age)}}} &\sim \text{N}^{+}\left(0, 1\right) \\
\sigma_{r^{\text{(age)}}} &\sim \text{N}^{+}\left(0, 1\right)
\end{aligned}
{:/}$$

The priors for the first 2 parameters are given by
		
$${::nomarkdown}
\begin{aligned}
\text{logit}\left(S^{\text{(age)}}_{1}\right) &\sim \text{N}(0, 1) \\
\text{logit}\left(S^{\text{(age)}}_{2}\right) &\sim \text{N}\left(\text{logit}\left(S^{\text{(age)}}_{1}\right), 1\right) \\
\text{logit}\left(r^{\text{(age)}}_{1}\right) &\sim \text{N}(0, 1) \\
\text{logit}\left(r^{\text{(age)}}_{2}\right) &\sim \text{N}\left(\text{logit}\left(r^{\text{(age)}}_{1}\right), 1\right)
\end{aligned}
{:/}$$

The priors for survival and recovery rate parameters when $t > 2$ are given by

$${::nomarkdown}
\begin{aligned}
\left(\text{logit}\left(S^{\text{(age)}}_{t}\right) - \text{logit}\left(S^{\text{(age)}}_{t-1}\right)\right) - \left(\text{logit}\left(S^{\text{(age)}}_{t-1}\right) - \text{logit}\left(S^{\text{(age)}}_{t-2}\right)\right) &\sim \text{N}\left(0,\sigma_{S^{\text{(age)}}}^{2}\right) \\
\left(\text{logit}\left(r^{\text{(age)}}_{t}\right) - \text{logit}\left(r^{\text{(age)}}_{t-1}\right)\right) - \left(\text{logit}\left(r^{\text{(age)}}_{t-1}\right) - \text{logit}\left(r^{\text{(age)}}_{t-2}\right)\right) &\sim \text{N}\left(0,\sigma_{r^{\text{(age)}}}^{2}\right) \end{aligned}
{:/}$$

These can be written as

$${::nomarkdown}
\begin{aligned}
\text{logit}\left(S^{\text{(age)}}_{t}\right) &\sim \text{N}\left(2\cdot\text{logit}\left(S^{\text{(age)}}_{t-1}\right) - \text{logit}\left(S^{\text{(age)}}_{t-2}\right), \sigma_{S^{\text{(age)}}}^{2}\right) \\
\text{logit}\left(r^{\text{(age)}}_{t}\right) &\sim \text{N}\left(2\cdot\text{logit}\left(r^{\text{(age)}}_{t-1}\right) - \text{logit}\left(r^{\text{(age)}}_{t-2}\right), \sigma_{r^{\text{(age)}}}^{2}\right)
\end{aligned}
{:/}$$

<div style="text-align: center;">
  <div class = "row">
      <img src="/assets/images/f0_RW2.png" width="400">
      <img src="/assets/images/r0_RW2.png" width="400">
      <img src="/assets/images/S0_RW2.png" width="400">
  </div>
</div>

<div style="text-align: center;">
  <div class = "row">
      <img src="/assets/images/f1_RW2.png" width="400">
      <img src="/assets/images/r1_RW2.png" width="400">
      <img src="/assets/images/S1_RW2.png" width="400">
  </div>
</div>

<div style="text-align: center;">
  <div class = "row">
      <img src="/assets/images/f2_RW2.png" width="400">
      <img src="/assets/images/r2_RW2.png" width="400">
      <img src="/assets/images/S2_RW2.png" width="400">
  </div>
</div>

The estimates of $\sigma$ are

<table style="margin: 0 auto; border-collapse: collapse; width: auto;">
<tr><th>Param</th><th>Age</th><th>Mean</th><th>Median</th><th>StdErr</th><th>Lower</th><th>Upper</th></tr>
<tr><td>r</td><td>a</td><td>0.03376615</td><td>0.02440113</td><td>0.03196621</td><td>0.000870397</td><td>0.11784049</td></tr>
<tr><td>r</td><td>j</td><td>0.10170912</td><td>0.09468576</td><td>0.04275921</td><td>0.038812181</td><td>0.20398297</td></tr>
<tr><td>r</td><td>p</td><td>0.02189553</td><td>0.01529642</td><td>0.02249487</td><td>0.000823826</td><td>0.08327095</td></tr>
<tr><td>s</td><td>a</td><td>0.05089639</td><td>0.03946132</td><td>0.03989246</td><td>0.00617383</td><td>0.15452881</td></tr>
<tr><td>s</td><td>j</td><td>0.0490313</td><td>0.03748885</td><td>0.04235184</td><td>0.002275359</td><td>0.15988431</td></tr>
<tr><td>s</td><td>p</td><td>0.03804243</td><td>0.02767054</td><td>0.03591204</td><td>0.002300109</td><td>0.13582211</td></tr>
</table>

###Goodness of fit

### Data frames

Data frames containing the estimates, standard errors, and 95% confidence interval bounds are below

<details>
<summary>M<sub>sep</sub> (GLM) [Brownie]</summary>
<table style="margin: 0 auto; border-collapse: collapse; width: auto;">
<tr><th>time</th><th>Param</th><th>Age</th><th>Estim</th><th>StdErr</th><th>Lower</th><th>Upper</th></tr>
<tr><td>1</td><td>f</td><td>0</td><td>0.020617639</td><td>0.003267762</td><td>0.015098264</td><td>0.028097136</td></tr>
<tr><td>1</td><td>f</td><td>1</td><td>0.025667032</td><td>0.002332928</td><td>0.021470163</td><td>0.030658578</td></tr>
<tr><td>1</td><td>f</td><td>2</td><td>0.021764591</td><td>0.002984161</td><td>0.016623377</td><td>0.028449859</td></tr>
<tr><td>1</td><td>S</td><td>0</td><td>0.623644812</td><td>0.093537825</td><td>0.431421207</td><td>0.783494876</td></tr>
<tr><td>1</td><td>S</td><td>1</td><td>0.508727202</td><td>0.062464879</td><td>0.388181765</td><td>0.628266269</td></tr>
<tr><td>1</td><td>S</td><td>2</td><td>0.351984281</td><td>0.056829035</td><td>0.249990476</td><td>0.469539321</td></tr>
<tr><td>2</td><td>f</td><td>0</td><td>0.018260871</td><td>0.002113026</td><td>0.014548934</td><td>0.022897847</td></tr>
<tr><td>2</td><td>f</td><td>1</td><td>0.019585424</td><td>0.001890824</td><td>0.01620348</td><td>0.023656265</td></tr>
<tr><td>2</td><td>f</td><td>2</td><td>0.021633875</td><td>0.002487741</td><td>0.017259314</td><td>0.027086655</td></tr>
<tr><td>2</td><td>S</td><td>0</td><td>0.810075397</td><td>0.108386372</td><td>0.517423306</td><td>0.944342857</td></tr>
<tr><td>2</td><td>S</td><td>1</td><td>0.630667555</td><td>0.07952999</td><td>0.466515564</td><td>0.769289185</td></tr>
<tr><td>2</td><td>S</td><td>2</td><td>0.546501259</td><td>0.067990653</td><td>0.413102752</td><td>0.67354037</td></tr>
<tr><td>3</td><td>f</td><td>0</td><td>0.011833505</td><td>0.001428588</td><td>0.00933719</td><td>0.014987118</td></tr>
<tr><td>3</td><td>f</td><td>1</td><td>0.017344092</td><td>0.001875256</td><td>0.014026735</td><td>0.021428962</td></tr>
<tr><td>3</td><td>f</td><td>2</td><td>0.017864627</td><td>0.002004658</td><td>0.014331698</td><td>0.022248806</td></tr>
<tr><td>3</td><td>S</td><td>0</td><td>0.707110333</td><td>0.090496529</td><td>0.506237451</td><td>0.850411273</td></tr>
<tr><td>3</td><td>S</td><td>1</td><td>0.538880488</td><td>0.066860676</td><td>0.40816894</td><td>0.664455471</td></tr>
<tr><td>3</td><td>S</td><td>2</td><td>0.401722734</td><td>0.055403209</td><td>0.299410064</td><td>0.513377889</td></tr>
<tr><td>4</td><td>f</td><td>0</td><td>0.015267504</td><td>0.00152213</td><td>0.012554032</td><td>0.018556452</td></tr>
<tr><td>4</td><td>f</td><td>1</td><td>0.016927385</td><td>0.001715462</td><td>0.013873534</td><td>0.020639383</td></tr>
<tr><td>4</td><td>f</td><td>2</td><td>0.015973421</td><td>0.00193753</td><td>0.012588188</td><td>0.020250347</td></tr>
<tr><td>4</td><td>S</td><td>0</td><td>0.707099957</td><td>0.086454123</td><td>0.515788359</td><td>0.845470314</td></tr>
<tr><td>4</td><td>S</td><td>1</td><td>0.573638118</td><td>0.06990295</td><td>0.434508553</td><td>0.702012584</td></tr>
<tr><td>4</td><td>S</td><td>2</td><td>0.347513217</td><td>0.050181518</td><td>0.256592429</td><td>0.451101573</td></tr>
<tr><td>5</td><td>f</td><td>0</td><td>0.01328921</td><td>0.001378604</td><td>0.010841313</td><td>0.016280728</td></tr>
<tr><td>5</td><td>f</td><td>1</td><td>0.014762704</td><td>0.001521132</td><td>0.012059608</td><td>0.018060609</td></tr>
<tr><td>5</td><td>f</td><td>2</td><td>0.024208649</td><td>0.002379535</td><td>0.019957866</td><td>0.029337695</td></tr>
<tr><td>5</td><td>S</td><td>0</td><td>0.511507223</td><td>0.059545492</td><td>0.396266232</td><td>0.625537842</td></tr>
<tr><td>5</td><td>S</td><td>1</td><td>0.414505753</td><td>0.045797245</td><td>0.328443429</td><td>0.506123353</td></tr>
<tr><td>5</td><td>S</td><td>2</td><td>0.450693545</td><td>0.060361282</td><td>0.337210857</td><td>0.56954885</td></tr>
<tr><td>6</td><td>f</td><td>0</td><td>0.019583563</td><td>0.001732982</td><td>0.016460489</td><td>0.023285155</td></tr>
<tr><td>6</td><td>f</td><td>1</td><td>0.015572416</td><td>0.001613381</td><td>0.012706705</td><td>0.019071939</td></tr>
<tr><td>6</td><td>f</td><td>2</td><td>0.018559196</td><td>0.002042997</td><td>0.014951295</td><td>0.023017377</td></tr>
<tr><td>6</td><td>S</td><td>0</td><td>0.831766561</td><td>0.091943527</td><td>0.576971843</td><td>0.947152207</td></tr>
<tr><td>6</td><td>S</td><td>1</td><td>0.563190653</td><td>0.064258545</td><td>0.435894065</td><td>0.68267352</td></tr>
<tr><td>6</td><td>S</td><td>2</td><td>0.388572087</td><td>0.052482068</td><td>0.29187795</td><td>0.494911179</td></tr>
<tr><td>7</td><td>f</td><td>0</td><td>0.013956324</td><td>0.001333262</td><td>0.011570497</td><td>0.016825733</td></tr>
<tr><td>7</td><td>f</td><td>1</td><td>0.014686379</td><td>0.001746668</td><td>0.011628278</td><td>0.018533645</td></tr>
<tr><td>7</td><td>f</td><td>2</td><td>0.017340146</td><td>0.002256313</td><td>0.013429569</td><td>0.022363636</td></tr>
<tr><td>7</td><td>S</td><td>0</td><td>0.803919987</td><td>0.093498416</td><td>0.561787612</td><td>0.929138708</td></tr>
<tr><td>7</td><td>S</td><td>1</td><td>0.615731999</td><td>0.076063911</td><td>0.460426094</td><td>0.750554265</td></tr>
<tr><td>7</td><td>S</td><td>2</td><td>0.316624272</td><td>0.053640654</td><td>0.221796384</td><td>0.429613166</td></tr>
<tr><td>8</td><td>f</td><td>0</td><td>0.011642502</td><td>0.001147144</td><td>0.009595899</td><td>0.014119379</td></tr>
<tr><td>8</td><td>f</td><td>1</td><td>0.013748714</td><td>0.001713836</td><td>0.010764335</td><td>0.017545826</td></tr>
<tr><td>8</td><td>f</td><td>2</td><td>0.016437136</td><td>0.002198778</td><td>0.012639517</td><td>0.021351099</td></tr>
<tr><td>8</td><td>S</td><td>0</td><td>0.787121773</td><td>0.093328794</td><td>0.553784753</td><td>0.916777779</td></tr>
<tr><td>8</td><td>S</td><td>1</td><td>0.696585161</td><td>0.086956924</td><td>0.506172566</td><td>0.837192655</td></tr>
<tr><td>8</td><td>S</td><td>2</td><td>0.455850798</td><td>0.067894555</td><td>0.328818443</td><td>0.588899561</td></tr>
<tr><td>9</td><td>f</td><td>0</td><td>0.012445191</td><td>0.001185437</td><td>0.010323598</td><td>0.014996184</td></tr>
<tr><td>9</td><td>f</td><td>1</td><td>0.017680522</td><td>0.001927249</td><td>0.014273893</td><td>0.021882136</td></tr>
<tr><td>9</td><td>f</td><td>2</td><td>0.018507279</td><td>0.002240715</td><td>0.014590505</td><td>0.023450478</td></tr>
<tr><td>9</td><td>S</td><td>0</td><td>0.703359342</td><td>0.083596595</td><td>0.519501198</td><td>0.8387083</td></tr>
<tr><td>9</td><td>S</td><td>1</td><td>0.649213894</td><td>0.081839014</td><td>0.477824364</td><td>0.789170383</td></tr>
<tr><td>9</td><td>S</td><td>2</td><td>0.395461095</td><td>0.058905959</td><td>0.287543017</td><td>0.514625785</td></tr>
<tr><td>10</td><td>f</td><td>0</td><td>0.011534258</td><td>0.001113314</td><td>0.009544274</td><td>0.013933319</td></tr>
<tr><td>10</td><td>f</td><td>1</td><td>0.020954757</td><td>0.002105161</td><td>0.017202769</td><td>0.025503832</td></tr>
<tr><td>10</td><td>f</td><td>2</td><td>0.017788162</td><td>0.002400121</td><td>0.01364663</td><td>0.023157074</td></tr>
<tr><td>10</td><td>S</td><td>0</td><td>0.763238839</td><td>0.084751734</td><td>0.5624888</td><td>0.88990483</td></tr>
<tr><td>10</td><td>S</td><td>1</td><td>0.869077573</td><td>0.098588005</td><td>0.548485706</td><td>0.973171632</td></tr>
<tr><td>10</td><td>S</td><td>2</td><td>0.34115297</td><td>0.0524372</td><td>0.246862011</td><td>0.449942781</td></tr>
<tr><td>11</td><td>f</td><td>0</td><td>0.010437097</td><td>0.000947785</td><td>0.008733993</td><td>0.012468124</td></tr>
<tr><td>11</td><td>f</td><td>1</td><td>0.01944119</td><td>0.002253161</td><td>0.015483298</td><td>0.024385752</td></tr>
<tr><td>11</td><td>f</td><td>2</td><td>0.015353567</td><td>0.002353662</td><td>0.011361674</td><td>0.020718607</td></tr>
<tr><td>11</td><td>S</td><td>0</td><td>0.989408815</td><td>0.055447486</td><td>0.002918271</td><td>0.999999665</td></tr>
<tr><td>11</td><td>S</td><td>1</td><td>0.840936409</td><td>0.115844717</td><td>0.491939848</td><td>0.966517205</td></tr>
<tr><td>11</td><td>S</td><td>2</td><td>0.373708005</td><td>0.067039575</td><td>0.253932186</td><td>0.511264432</td></tr>
<tr><td>12</td><td>f</td><td>0</td><td>0.007788949</td><td>0.000675737</td><td>0.006570284</td><td>0.009231553</td></tr>
<tr><td>12</td><td>f</td><td>1</td><td>0.017093526</td><td>0.001942648</td><td>0.013674706</td><td>0.021348587</td></tr>
<tr><td>12</td><td>f</td><td>2</td><td>0.018529111</td><td>0.002431062</td><td>0.014319349</td><td>0.023946437</td></tr>
<tr><td>12</td><td>S</td><td>0</td><td>0.589115552</td><td>0.069603322</td><td>0.449353535</td><td>0.715836834</td></tr>
<tr><td>12</td><td>S</td><td>1</td><td>0.801099498</td><td>0.10912239</td><td>0.512718904</td><td>0.939087732</td></tr>
<tr><td>12</td><td>S</td><td>2</td><td>0.271068805</td><td>0.052186667</td><td>0.181402742</td><td>0.384251629</td></tr>
<tr><td>13</td><td>f</td><td>0</td><td>0.009897492</td><td>0.001016552</td><td>0.008091256</td><td>0.01210202</td></tr>
<tr><td>13</td><td>f</td><td>1</td><td>0.014278584</td><td>0.001646431</td><td>0.011386332</td><td>0.017892203</td></tr>
<tr><td>13</td><td>f</td><td>2</td><td>0.019095235</td><td>0.002483025</td><td>0.014790573</td><td>0.02462142</td></tr>
<tr><td>13</td><td>S</td><td>0</td><td>0.61677698</td><td>0.082500703</td><td>0.448129225</td><td>0.761335085</td></tr>
<tr><td>13</td><td>S</td><td>1</td><td>0.734864237</td><td>0.100840559</td><td>0.501257055</td><td>0.884306502</td></tr>
<tr><td>13</td><td>S</td><td>2</td><td>0.432247204</td><td>0.072563163</td><td>0.29896633</td><td>0.576115723</td></tr>
<tr><td>14</td><td>f</td><td>0</td><td>0.009770496</td><td>0.001040096</td><td>0.007928938</td><td>0.012034581</td></tr>
<tr><td>14</td><td>f</td><td>1</td><td>0.015601056</td><td>0.00166156</td><td>0.012657752</td><td>0.019215446</td></tr>
<tr><td>14</td><td>f</td><td>2</td><td>0.018617522</td><td>0.0023063</td><td>0.014596551</td><td>0.023719504</td></tr>
<tr><td>14</td><td>S</td><td>0</td><td>0.688350373</td><td>0.095264297</td><td>0.480518882</td><td>0.840613613</td></tr>
<tr><td>14</td><td>S</td><td>1</td><td>0.727206681</td><td>0.101385315</td><td>0.494698197</td><td>0.878915551</td></tr>
<tr><td>14</td><td>S</td><td>2</td><td>0.308111289</td><td>0.056092643</td><td>0.210039224</td><td>0.427211054</td></tr>
<tr><td>15</td><td>f</td><td>0</td><td>0.009377257</td><td>0.001030907</td><td>0.007557995</td><td>0.011629296</td></tr>
<tr><td>15</td><td>f</td><td>1</td><td>0.015135906</td><td>0.001665475</td><td>0.012195518</td><td>0.018771762</td></tr>
<tr><td>15</td><td>f</td><td>2</td><td>0.016986404</td><td>0.002174007</td><td>0.013211146</td><td>0.02181664</td></tr>
<tr><td>15</td><td>S</td><td>0</td><td>0.542018494</td><td>0.07431833</td><td>0.396916796</td><td>0.680326167</td></tr>
<tr><td>15</td><td>S</td><td>1</td><td>0.567901257</td><td>0.07925805</td><td>0.411016516</td><td>0.712253007</td></tr>
<tr><td>15</td><td>S</td><td>2</td><td>0.47048715</td><td>0.074628789</td><td>0.330632868</td><td>0.615134381</td></tr>
<tr><td>16</td><td>f</td><td>0</td><td>0.011356858</td><td>0.001183073</td><td>0.009257366</td><td>0.013925805</td></tr>
<tr><td>16</td><td>f</td><td>1</td><td>0.014091081</td><td>0.001566088</td><td>0.011329285</td><td>0.017514208</td></tr>
<tr><td>16</td><td>f</td><td>2</td><td>0.016236566</td><td>0.002046445</td><td>0.012676676</td><td>0.020775117</td></tr>
<tr><td>16</td><td>S</td><td>0</td><td>0.643358583</td><td>0.083855971</td><td>0.46845558</td><td>0.786891389</td></tr>
<tr><td>16</td><td>S</td><td>1</td><td>0.573587097</td><td>0.07823108</td><td>0.418141099</td><td>0.715736889</td></tr>
<tr><td>16</td><td>S</td><td>2</td><td>0.364653849</td><td>0.063127898</td><td>0.251754509</td><td>0.494708655</td></tr>
<tr><td>17</td><td>f</td><td>0</td><td>0.011657978</td><td>0.001160646</td><td>0.009589282</td><td>0.014166569</td></tr>
<tr><td>17</td><td>f</td><td>1</td><td>0.010877028</td><td>0.001311255</td><td>0.008585591</td><td>0.013771538</td></tr>
<tr><td>17</td><td>f</td><td>2</td><td>0.016272045</td><td>0.002197802</td><td>0.012480741</td><td>0.021190329</td></tr>
<tr><td>17</td><td>S</td><td>0</td><td>0.770597409</td><td>0.099161766</td><td>0.52803062</td><td>0.909795044</td></tr>
<tr><td>17</td><td>S</td><td>1</td><td>0.617431873</td><td>0.087792622</td><td>0.437868859</td><td>0.769792459</td></tr>
<tr><td>17</td><td>S</td><td>2</td><td>0.43756572</td><td>0.082550755</td><td>0.287307629</td><td>0.600224734</td></tr>
<tr><td>18</td><td>f</td><td>0</td><td>0.010246432</td><td>0.001043328</td><td>0.008391021</td><td>0.012506934</td></tr>
<tr><td>18</td><td>f</td><td>1</td><td>0.011631331</td><td>0.001384349</td><td>0.009208512</td><td>0.014682163</td></tr>
<tr><td>18</td><td>f</td><td>2</td><td>0.016666997</td><td>0.002188466</td><td>0.012878448</td><td>0.021545724</td></tr>
<tr><td>18</td><td>S</td><td>0</td><td>0.976417225</td><td>0.115093011</td><td>0.002298469</td><td>0.999998656</td></tr>
<tr><td>18</td><td>S</td><td>1</td><td>0.846816512</td><td>0.150404159</td><td>0.362920952</td><td>0.981700343</td></tr>
<tr><td>18</td><td>S</td><td>2</td><td>0.425531526</td><td>0.085724752</td><td>0.27141907</td><td>0.595613855</td></tr>
<tr><td>19</td><td>f</td><td>0</td><td>0.006612834</td><td>0.00081828</td><td>0.005187741</td><td>0.008426088</td></tr>
<tr><td>19</td><td>f</td><td>1</td><td>0.010305027</td><td>0.001300065</td><td>0.008045136</td><td>0.013191285</td></tr>
<tr><td>19</td><td>f</td><td>2</td><td>0.01431259</td><td>0.002221747</td><td>0.010551612</td><td>0.019387849</td></tr>
<tr><td>19</td><td>S</td><td>0</td><td>0.535137645</td><td>0.107333467</td><td>0.330729348</td><td>0.728386846</td></tr>
<tr><td>19</td><td>S</td><td>1</td><td>0.786940284</td><td>0.166514544</td><td>0.345253742</td><td>0.962785347</td></tr>
<tr><td>19</td><td>S</td><td>2</td><td>0.566005353</td><td>0.121015508</td><td>0.331808372</td><td>0.774021548</td></tr>
<tr><td>20</td><td>f</td><td>0</td><td>0.008931079</td><td>0.001368628</td><td>0.006611505</td><td>0.012054574</td></tr>
<tr><td>20</td><td>f</td><td>1</td><td>0.017911748</td><td>0.001600518</td><td>0.015030104</td><td>0.021333907</td></tr>
<tr><td>20</td><td>f</td><td>2</td><td>0.017358008</td><td>0.002056777</td><td>0.013754533</td><td>0.021884589</td></tr>
<tr><td>20</td><td>S</td><td>0</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td></tr>
<tr><td>20</td><td>S</td><td>1</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td></tr>
<tr><td>20</td><td>S</td><td>2</td><td>0.998771002</td><td>0.007151514</td><td>0.008845945</td><td>0.999999986</td></tr>
</table>
</details>

<details>
<summary>M<sub>comb</sub> (GLM) [Brownie]</summary>
<table style="margin: 0 auto; border-collapse: collapse; width: auto;">
<tr><th>time</th><th>Param</th><th>Age</th><th>Estim</th><th>StdErr</th><th>Lower</th><th>Upper</th></tr>
<tr><td>1</td><td>f</td><td>0</td><td>0.020620572</td><td>0.00326796</td><td>0.015100796</td><td>0.02810042</td></tr>
<tr><td>1</td><td>f</td><td>1</td><td>0.02724635</td><td>0.002517014</td><td>0.022724003</td><td>0.032638639</td></tr>
<tr><td>1</td><td>f</td><td>2</td><td>0.014888183</td><td>0.004525355</td><td>0.008187341</td><td>0.026924381</td></tr>
<tr><td>1</td><td>S</td><td>0</td><td>0.624603463</td><td>0.093868842</td><td>0.431550391</td><td>0.784789104</td></tr>
<tr><td>1</td><td>S</td><td>1</td><td>0.480583989</td><td>0.061564052</td><td>0.36329432</td><td>0.600053485</td></tr>
<tr><td>1</td><td>S</td><td>2</td><td>0.481336746</td><td>0.098501786</td><td>0.299848546</td><td>0.667887004</td></tr>
<tr><td>2</td><td>f</td><td>0</td><td>0.018227759</td><td>0.00211285</td><td>0.014516836</td><td>0.022865291</td></tr>
<tr><td>2</td><td>f</td><td>1</td><td>0.021081746</td><td>0.00216284</td><td>0.017234567</td><td>0.025765194</td></tr>
<tr><td>2</td><td>f</td><td>2</td><td>0.016706641</td><td>0.003950938</td><td>0.010493033</td><td>0.026501185</td></tr>
<tr><td>2</td><td>S</td><td>0</td><td>0.807576247</td><td>0.10904608</td><td>0.51473415</td><td>0.94319889</td></tr>
<tr><td>2</td><td>S</td><td>1</td><td>0.595142625</td><td>0.078567141</td><td>0.436878261</td><td>0.735823673</td></tr>
<tr><td>2</td><td>S</td><td>2</td><td>0.650237698</td><td>0.102701007</td><td>0.434134475</td><td>0.818344376</td></tr>
<tr><td>3</td><td>f</td><td>0</td><td>0.011853892</td><td>0.001434927</td><td>0.009347261</td><td>0.015022526</td></tr>
<tr><td>3</td><td>f</td><td>1</td><td>0.017701244</td><td>0.002096998</td><td>0.01402709</td><td>0.022315998</td></tr>
<tr><td>3</td><td>f</td><td>2</td><td>0.017064733</td><td>0.002853268</td><td>0.012285962</td><td>0.023657744</td></tr>
<tr><td>3</td><td>S</td><td>0</td><td>0.709443393</td><td>0.091744091</td><td>0.505085456</td><td>0.853837522</td></tr>
<tr><td>3</td><td>S</td><td>1</td><td>0.532050964</td><td>0.069281535</td><td>0.397228031</td><td>0.66235089</td></tr>
<tr><td>3</td><td>S</td><td>2</td><td>0.420540405</td><td>0.075298063</td><td>0.283698803</td><td>0.570790444</td></tr>
<tr><td>4</td><td>f</td><td>0</td><td>0.015249382</td><td>0.001526901</td><td>0.01252851</td><td>0.018550059</td></tr>
<tr><td>4</td><td>f</td><td>1</td><td>0.017466549</td><td>0.001878009</td><td>0.014142422</td><td>0.021554918</td></tr>
<tr><td>4</td><td>f</td><td>2</td><td>0.014608305</td><td>0.002655717</td><td>0.010220874</td><td>0.020839437</td></tr>
<tr><td>4</td><td>S</td><td>0</td><td>0.707270982</td><td>0.087407716</td><td>0.513669792</td><td>0.846789624</td></tr>
<tr><td>4</td><td>S</td><td>1</td><td>0.561278578</td><td>0.070722705</td><td>0.421512255</td><td>0.691954766</td></tr>
<tr><td>4</td><td>S</td><td>2</td><td>0.380988487</td><td>0.069834945</td><td>0.25621358</td><td>0.523740411</td></tr>
<tr><td>5</td><td>f</td><td>0</td><td>0.013278484</td><td>0.0013824</td><td>0.010824699</td><td>0.016279347</td></tr>
<tr><td>5</td><td>f</td><td>1</td><td>0.014326386</td><td>0.001642738</td><td>0.011438877</td><td>0.017929566</td></tr>
<tr><td>5</td><td>f</td><td>2</td><td>0.025387216</td><td>0.002992743</td><td>0.020136771</td><td>0.031962002</td></tr>
<tr><td>5</td><td>S</td><td>0</td><td>0.511076352</td><td>0.059917452</td><td>0.395160576</td><td>0.625813044</td></tr>
<tr><td>5</td><td>S</td><td>1</td><td>0.422243771</td><td>0.047999706</td><td>0.331986652</td><td>0.518010938</td></tr>
<tr><td>5</td><td>S</td><td>2</td><td>0.420358019</td><td>0.072705952</td><td>0.287786865</td><td>0.565508595</td></tr>
<tr><td>6</td><td>f</td><td>0</td><td>0.019596401</td><td>0.001739481</td><td>0.016462414</td><td>0.02331287</td></tr>
<tr><td>6</td><td>f</td><td>1</td><td>0.015452418</td><td>0.001758787</td><td>0.01235815</td><td>0.019306292</td></tr>
<tr><td>6</td><td>f</td><td>2</td><td>0.018864571</td><td>0.002659881</td><td>0.014299951</td><td>0.024849511</td></tr>
<tr><td>6</td><td>S</td><td>0</td><td>0.832172207</td><td>0.093586613</td><td>0.571436389</td><td>0.948558</td></tr>
<tr><td>6</td><td>S</td><td>1</td><td>0.566362655</td><td>0.066719844</td><td>0.434027541</td><td>0.689865268</td></tr>
<tr><td>6</td><td>S</td><td>2</td><td>0.380841636</td><td>0.065195945</td><td>0.263493067</td><td>0.513980531</td></tr>
<tr><td>7</td><td>f</td><td>0</td><td>0.013955211</td><td>0.001343914</td><td>0.011552063</td><td>0.016849758</td></tr>
<tr><td>7</td><td>f</td><td>1</td><td>0.014821229</td><td>0.001883984</td><td>0.011547703</td><td>0.019004889</td></tr>
<tr><td>7</td><td>f</td><td>2</td><td>0.016990285</td><td>0.002803399</td><td>0.012285584</td><td>0.023453847</td></tr>
<tr><td>7</td><td>S</td><td>0</td><td>0.805652042</td><td>0.095316687</td><td>0.556962872</td><td>0.931830811</td></tr>
<tr><td>7</td><td>S</td><td>1</td><td>0.613351603</td><td>0.077836335</td><td>0.454655882</td><td>0.751143706</td></tr>
<tr><td>7</td><td>S</td><td>2</td><td>0.325103631</td><td>0.068659287</td><td>0.206894325</td><td>0.470762893</td></tr>
<tr><td>8</td><td>f</td><td>0</td><td>0.011621286</td><td>0.001151244</td><td>0.0095684</td><td>0.014108341</td></tr>
<tr><td>8</td><td>f</td><td>1</td><td>0.014250618</td><td>0.001945327</td><td>0.010900072</td><td>0.018611701</td></tr>
<tr><td>8</td><td>f</td><td>2</td><td>0.015146578</td><td>0.003015126</td><td>0.01024282</td><td>0.02234501</td></tr>
<tr><td>8</td><td>S</td><td>0</td><td>0.784604153</td><td>0.093474363</td><td>0.551967533</td><td>0.91503945</td></tr>
<tr><td>8</td><td>S</td><td>1</td><td>0.681054016</td><td>0.088072204</td><td>0.490984134</td><td>0.825390867</td></tr>
<tr><td>8</td><td>S</td><td>2</td><td>0.488172887</td><td>0.088559726</td><td>0.322563844</td><td>0.656419735</td></tr>
<tr><td>9</td><td>f</td><td>0</td><td>0.012457201</td><td>0.001186488</td><td>0.01033371</td><td>0.015010432</td></tr>
<tr><td>9</td><td>f</td><td>1</td><td>0.018763421</td><td>0.002160632</td><td>0.014965683</td><td>0.02350189</td></tr>
<tr><td>9</td><td>f</td><td>2</td><td>0.015570454</td><td>0.003239987</td><td>0.010343642</td><td>0.023376074</td></tr>
<tr><td>9</td><td>S</td><td>0</td><td>0.704512186</td><td>0.083900772</td><td>0.51972434</td><td>0.840079234</td></tr>
<tr><td>9</td><td>S</td><td>1</td><td>0.619521319</td><td>0.082148658</td><td>0.451265093</td><td>0.763252446</td></tr>
<tr><td>9</td><td>S</td><td>2</td><td>0.466904523</td><td>0.088675018</td><td>0.303465967</td><td>0.637769385</td></tr>
<tr><td>10</td><td>f</td><td>0</td><td>0.011532145</td><td>0.001114148</td><td>0.009540839</td><td>0.013933219</td></tr>
<tr><td>10</td><td>f</td><td>1</td><td>0.019893838</td><td>0.002188409</td><td>0.016028387</td><td>0.024668123</td></tr>
<tr><td>10</td><td>f</td><td>2</td><td>0.020735111</td><td>0.003230454</td><td>0.015265145</td><td>0.028109183</td></tr>
<tr><td>10</td><td>S</td><td>0</td><td>0.760874473</td><td>0.084805053</td><td>0.560678467</td><td>0.888056739</td></tr>
<tr><td>10</td><td>S</td><td>1</td><td>0.895863641</td><td>0.103135304</td><td>0.496321451</td><td>0.986860277</td></tr>
<tr><td>10</td><td>S</td><td>2</td><td>0.283709408</td><td>0.0609293</td><td>0.180378362</td><td>0.416177607</td></tr>
<tr><td>11</td><td>f</td><td>0</td><td>0.010469273</td><td>0.00095267</td><td>0.008757691</td><td>0.012511142</td></tr>
<tr><td>11</td><td>f</td><td>1</td><td>0.020308373</td><td>0.002455981</td><td>0.016013981</td><td>0.025724264</td></tr>
<tr><td>11</td><td>f</td><td>2</td><td>0.012476139</td><td>0.003756021</td><td>0.006902755</td><td>0.022447836</td></tr>
<tr><td>11</td><td>S</td><td>0</td><td>0.986786501</td><td>0.060193371</td><td>0.008705843</td><td>0.999998425</td></tr>
<tr><td>11</td><td>S</td><td>1</td><td>0.795197669</td><td>0.115424863</td><td>0.49185254</td><td>0.939669114</td></tr>
<tr><td>11</td><td>S</td><td>2</td><td>0.449472291</td><td>0.109270016</td><td>0.255722929</td><td>0.659869487</td></tr>
<tr><td>12</td><td>f</td><td>0</td><td>0.007830095</td><td>0.000682289</td><td>0.006600054</td><td>0.009287233</td></tr>
<tr><td>12</td><td>f</td><td>1</td><td>0.017659289</td><td>0.00207724</td><td>0.014016932</td><td>0.022226791</td></tr>
<tr><td>12</td><td>f</td><td>2</td><td>0.016948813</td><td>0.003078569</td><td>0.011860454</td><td>0.024166784</td></tr>
<tr><td>12</td><td>S</td><td>0</td><td>0.591903727</td><td>0.070245765</td><td>0.450625704</td><td>0.71946809</td></tr>
<tr><td>12</td><td>S</td><td>1</td><td>0.781470184</td><td>0.109959798</td><td>0.50305812</td><td>0.926646126</td></tr>
<tr><td>12</td><td>S</td><td>2</td><td>0.310547963</td><td>0.076878672</td><td>0.182227799</td><td>0.476569353</td></tr>
<tr><td>13</td><td>f</td><td>0</td><td>0.009907266</td><td>0.001018998</td><td>0.008096929</td><td>0.012117419</td></tr>
<tr><td>13</td><td>f</td><td>1</td><td>0.015117346</td><td>0.001819383</td><td>0.011936008</td><td>0.019130197</td></tr>
<tr><td>13</td><td>f</td><td>2</td><td>0.016234973</td><td>0.003383466</td><td>0.010777739</td><td>0.024387316</td></tr>
<tr><td>13</td><td>S</td><td>0</td><td>0.616077065</td><td>0.082451968</td><td>0.447613661</td><td>0.760637477</td></tr>
<tr><td>13</td><td>S</td><td>1</td><td>0.703043712</td><td>0.100202896</td><td>0.480288149</td><td>0.858459855</td></tr>
<tr><td>13</td><td>S</td><td>2</td><td>0.515401672</td><td>0.108648868</td><td>0.311956148</td><td>0.713867486</td></tr>
<tr><td>14</td><td>f</td><td>0</td><td>0.009785409</td><td>0.001040642</td><td>0.007942698</td><td>0.012050437</td></tr>
<tr><td>14</td><td>f</td><td>1</td><td>0.016294586</td><td>0.001784785</td><td>0.013141737</td><td>0.020188364</td></tr>
<tr><td>14</td><td>f</td><td>2</td><td>0.016262046</td><td>0.002998225</td><td>0.011319286</td><td>0.023312255</td></tr>
<tr><td>14</td><td>S</td><td>0</td><td>0.688268154</td><td>0.095091021</td><td>0.480849708</td><td>0.840333155</td></tr>
<tr><td>14</td><td>S</td><td>1</td><td>0.697509992</td><td>0.100000409</td><td>0.476644497</td><td>0.853763274</td></tr>
<tr><td>14</td><td>S</td><td>2</td><td>0.378368923</td><td>0.088093103</td><td>0.226082042</td><td>0.559125511</td></tr>
<tr><td>15</td><td>f</td><td>0</td><td>0.009392125</td><td>0.00103012</td><td>0.00757381</td><td>0.011641859</td></tr>
<tr><td>15</td><td>f</td><td>1</td><td>0.016034681</td><td>0.001849049</td><td>0.012785968</td><td>0.020092041</td></tr>
<tr><td>15</td><td>f</td><td>2</td><td>0.013723335</td><td>0.003375844</td><td>0.008461811</td><td>0.022183261</td></tr>
<tr><td>15</td><td>S</td><td>0</td><td>0.542048409</td><td>0.074161757</td><td>0.397240197</td><td>0.680084752</td></tr>
<tr><td>15</td><td>S</td><td>1</td><td>0.53623714</td><td>0.079313568</td><td>0.382276073</td><td>0.683586841</td></tr>
<tr><td>15</td><td>S</td><td>2</td><td>0.573831705</td><td>0.121122372</td><td>0.337766223</td><td>0.780446162</td></tr>
<tr><td>16</td><td>f</td><td>0</td><td>0.011370943</td><td>0.001183932</td><td>0.009269819</td><td>0.013941614</td></tr>
<tr><td>16</td><td>f</td><td>1</td><td>0.015010764</td><td>0.001720244</td><td>0.011986639</td><td>0.018783345</td></tr>
<tr><td>16</td><td>f</td><td>2</td><td>0.01319379</td><td>0.002890717</td><td>0.008578347</td><td>0.020241798</td></tr>
<tr><td>16</td><td>S</td><td>0</td><td>0.643225149</td><td>0.083928678</td><td>0.46818589</td><td>0.786877993</td></tr>
<tr><td>16</td><td>S</td><td>1</td><td>0.540539191</td><td>0.077710135</td><td>0.389178783</td><td>0.684772586</td></tr>
<tr><td>16</td><td>S</td><td>2</td><td>0.465646312</td><td>0.103891623</td><td>0.277678728</td><td>0.66390429</td></tr>
<tr><td>17</td><td>f</td><td>0</td><td>0.011669547</td><td>0.001163068</td><td>0.009596746</td><td>0.014183641</td></tr>
<tr><td>17</td><td>f</td><td>1</td><td>0.011012977</td><td>0.001402732</td><td>0.008577199</td><td>0.014130616</td></tr>
<tr><td>17</td><td>f</td><td>2</td><td>0.01569785</td><td>0.002883087</td><td>0.010942197</td><td>0.022473423</td></tr>
<tr><td>17</td><td>S</td><td>0</td><td>0.768951477</td><td>0.100335395</td><td>0.523857083</td><td>0.909644289</td></tr>
<tr><td>17</td><td>S</td><td>1</td><td>0.606923874</td><td>0.089986192</td><td>0.424359949</td><td>0.763813277</td></tr>
<tr><td>17</td><td>S</td><td>2</td><td>0.464514163</td><td>0.117027546</td><td>0.256484162</td><td>0.685671141</td></tr>
<tr><td>18</td><td>f</td><td>0</td><td>0.010275282</td><td>0.001057137</td><td>0.008397177</td><td>0.012568117</td></tr>
<tr><td>18</td><td>f</td><td>1</td><td>0.011446412</td><td>0.001445249</td><td>0.008934097</td><td>0.014654757</td></tr>
<tr><td>18</td><td>f</td><td>2</td><td>0.017492654</td><td>0.002917075</td><td>0.012604763</td><td>0.024229465</td></tr>
<tr><td>18</td><td>S</td><td>0</td><td>0.979444123</td><td>0.130051059</td><td>0.000151199</td><td>0.999999933</td></tr>
<tr><td>18</td><td>S</td><td>1</td><td>0.858541182</td><td>0.15739088</td><td>0.323696208</td><td>0.987172949</td></tr>
<tr><td>18</td><td>S</td><td>2</td><td>0.386850339</td><td>0.119428121</td><td>0.190398053</td><td>0.628615882</td></tr>
<tr><td>19</td><td>f</td><td>0</td><td>0.006617569</td><td>0.000854957</td><td>0.005136177</td><td>0.008522568</td></tr>
<tr><td>19</td><td>f</td><td>1</td><td>0.010775288</td><td>0.001396166</td><td>0.008355939</td><td>0.013885319</td></tr>
<tr><td>19</td><td>f</td><td>2</td><td>0.011178328</td><td>0.003789609</td><td>0.005740135</td><td>0.021656436</td></tr>
<tr><td>19</td><td>S</td><td>0</td><td>0.543224032</td><td>0.112473712</td><td>0.328471886</td><td>0.743027155</td></tr>
<tr><td>19</td><td>S</td><td>1</td><td>0.74757256</td><td>0.172103294</td><td>0.331406462</td><td>0.946507922</td></tr>
<tr><td>19</td><td>S</td><td>2</td><td>0.747192979</td><td>0.236785853</td><td>0.20210064</td><td>0.971821443</td></tr>
<tr><td>20</td><td>f</td><td>0</td><td>0.00881416</td><td>0.001380322</td><td>0.006482062</td><td>0.01197518</td></tr>
<tr><td>20</td><td>f</td><td>1</td><td>0.0277971</td><td>0.001979661</td><td>0.024169162</td><td>0.031951781</td></tr>
<tr><td>20</td><td>f</td><td>2</td><td>5.22358E-05</td><td>0.000297709</td><td>7.3514E-10</td><td>0.78777731</td></tr>
<tr><td>20</td><td>S</td><td>0</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td></tr>
<tr><td>20</td><td>S</td><td>1</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td></tr>
<tr><td>20</td><td>S</td><td>2</td><td>0.99968889</td><td>0.001820684</td><td>0.03233041</td><td>0.999999997</td></tr>
</table>
</details>

The tables below contain final $f$ and $f'$ estimates at the bottom.

<details>
<summary>M<sub>sep</sub> (GLM) [Seber]</summary>
<table style="margin: 0 auto; border-collapse: collapse; width: auto;">
<tr><th>time</th><th>Param</th><th>Age</th><th>Estim</th><th>StdErr</th><th>Lower</th><th>Upper</th></tr>
<tr><td>1</td><td>r</td><td>0</td><td>0.05524551</td><td>0.016310503</td><td>0.030719989</td><td>0.09738393</td></tr>
<tr><td>1</td><td>r</td><td>1</td><td>0.05227175</td><td>0.008388743</td><td>0.03807027</td><td>0.07137776</td></tr>
<tr><td>1</td><td>r</td><td>2</td><td>0.03367075</td><td>0.005452455</td><td>0.024474943</td><td>0.04615815</td></tr>
<tr><td>1</td><td>S</td><td>0</td><td>0.62641053</td><td>0.094403929</td><td>0.431970399</td><td>0.78709675</td></tr>
<tr><td>1</td><td>S</td><td>1</td><td>0.50954237</td><td>0.062827738</td><td>0.388273615</td><td>0.62969838</td></tr>
<tr><td>1</td><td>S</td><td>2</td><td>0.3546265</td><td>0.057203227</td><td>0.25187272</td><td>0.47280713</td></tr>
<tr><td>2</td><td>r</td><td>0</td><td>0.09427317</td><td>0.057293314</td><td>0.027180748</td><td>0.27940942</td></tr>
<tr><td>2</td><td>r</td><td>1</td><td>0.05279725</td><td>0.012903746</td><td>0.032521787</td><td>0.08460787</td></tr>
<tr><td>2</td><td>r</td><td>2</td><td>0.04784009</td><td>0.008980648</td><td>0.033012763</td><td>0.06885277</td></tr>
<tr><td>2</td><td>S</td><td>0</td><td>0.80662301</td><td>0.109131359</td><td>0.514226634</td><td>0.94264922</td></tr>
<tr><td>2</td><td>S</td><td>1</td><td>0.6289704</td><td>0.079854919</td><td>0.464339841</td><td>0.76825569</td></tr>
<tr><td>2</td><td>S</td><td>2</td><td>0.54753724</td><td>0.068220897</td><td>0.413623598</td><td>0.67490523</td></tr>
<tr><td>3</td><td>r</td><td>0</td><td>0.04074396</td><td>0.015751267</td><td>0.018914079</td><td>0.08557147</td></tr>
<tr><td>3</td><td>r</td><td>1</td><td>0.03761295</td><td>0.007189248</td><td>0.025797548</td><td>0.05453691</td></tr>
<tr><td>3</td><td>r</td><td>2</td><td>0.02986488</td><td>0.004314621</td><td>0.022474717</td><td>0.03958667</td></tr>
<tr><td>3</td><td>S</td><td>0</td><td>0.70905381</td><td>0.091893381</td><td>0.50443097</td><td>0.85369266</td></tr>
<tr><td>3</td><td>S</td><td>1</td><td>0.53867265</td><td>0.067076824</td><td>0.407563475</td><td>0.66464143</td></tr>
<tr><td>3</td><td>S</td><td>2</td><td>0.40187566</td><td>0.055425493</td><td>0.299517261</td><td>0.51356812</td></tr>
<tr><td>4</td><td>r</td><td>0</td><td>0.05226922</td><td>0.018500782</td><td>0.025839555</td><td>0.10287727</td></tr>
<tr><td>4</td><td>r</td><td>1</td><td>0.03958764</td><td>0.007840128</td><td>0.026778462</td><td>0.05815779</td></tr>
<tr><td>4</td><td>r</td><td>2</td><td>0.02461991</td><td>0.003512182</td><td>0.018597866</td><td>0.03252728</td></tr>
<tr><td>4</td><td>S</td><td>0</td><td>0.70800012</td><td>0.087301824</td><td>0.514498213</td><td>0.84727322</td></tr>
<tr><td>4</td><td>S</td><td>1</td><td>0.57211239</td><td>0.06962666</td><td>0.433645236</td><td>0.70013561</td></tr>
<tr><td>4</td><td>S</td><td>2</td><td>0.3481716</td><td>0.05032984</td><td>0.256974935</td><td>0.45204274</td></tr>
<tr><td>5</td><td>r</td><td>0</td><td>0.02722295</td><td>0.005347938</td><td>0.01848911</td><td>0.03991468</td></tr>
<tr><td>5</td><td>r</td><td>1</td><td>0.02526435</td><td>0.003416551</td><td>0.019365686</td><td>0.03289943</td></tr>
<tr><td>5</td><td>r</td><td>2</td><td>0.04411383</td><td>0.006448571</td><td>0.033066751</td><td>0.0586278</td></tr>
<tr><td>5</td><td>S</td><td>0</td><td>0.51236149</td><td>0.059384406</td><td>0.397377929</td><td>0.62605149</td></tr>
<tr><td>5</td><td>S</td><td>1</td><td>0.41553617</td><td>0.045706515</td><td>0.329600952</td><td>0.50693455</td></tr>
<tr><td>5</td><td>S</td><td>2</td><td>0.45087381</td><td>0.060421162</td><td>0.337275306</td><td>0.56983515</td></tr>
<tr><td>6</td><td>r</td><td>0</td><td>0.11453432</td><td>0.06444119</td><td>0.035893382</td><td>0.310062</td></tr>
<tr><td>6</td><td>r</td><td>1</td><td>0.03570303</td><td>0.006622739</td><td>0.024766248</td><td>0.05121586</td></tr>
<tr><td>6</td><td>r</td><td>2</td><td>0.03027378</td><td>0.004184495</td><td>0.023064996</td><td>0.03964419</td></tr>
<tr><td>6</td><td>S</td><td>0</td><td>0.82928527</td><td>0.088742593</td><td>0.587093416</td><td>0.94316978</td></tr>
<tr><td>6</td><td>S</td><td>1</td><td>0.56327531</td><td>0.063392383</td><td>0.437670971</td><td>0.6812562</td></tr>
<tr><td>6</td><td>S</td><td>2</td><td>0.38713889</td><td>0.052263389</td><td>0.290883438</td><td>0.49309735</td></tr>
<tr><td>7</td><td>r</td><td>0</td><td>0.07205727</td><td>0.036951004</td><td>0.025614566</td><td>0.18658236</td></tr>
<tr><td>7</td><td>r</td><td>1</td><td>0.03812837</td><td>0.008909461</td><td>0.024031364</td><td>0.05998653</td></tr>
<tr><td>7</td><td>r</td><td>2</td><td>0.02548076</td><td>0.003843107</td><td>0.018939865</td><td>0.03420181</td></tr>
<tr><td>7</td><td>S</td><td>0</td><td>0.80635382</td><td>0.089670729</td><td>0.574667594</td><td>0.92771173</td></tr>
<tr><td>7</td><td>S</td><td>1</td><td>0.61488283</td><td>0.074419898</td><td>0.463045917</td><td>0.74722273</td></tr>
<tr><td>7</td><td>S</td><td>2</td><td>0.3163</td><td>0.053706068</td><td>0.221389409</td><td>0.42945665</td></tr>
<tr><td>8</td><td>r</td><td>0</td><td>0.0533765</td><td>0.024421867</td><td>0.021397014</td><td>0.12695136</td></tr>
<tr><td>8</td><td>r</td><td>1</td><td>0.04473688</td><td>0.014175499</td><td>0.023861765</td><td>0.08233392</td></tr>
<tr><td>8</td><td>r</td><td>2</td><td>0.03036581</td><td>0.005529936</td><td>0.021212657</td><td>0.04329381</td></tr>
<tr><td>8</td><td>S</td><td>0</td><td>0.78244584</td><td>0.087996347</td><td>0.56629925</td><td>0.90831112</td></tr>
<tr><td>8</td><td>S</td><td>1</td><td>0.69311009</td><td>0.085331606</td><td>0.507102679</td><td>0.83215627</td></tr>
<tr><td>8</td><td>S</td><td>2</td><td>0.45749188</td><td>0.068225367</td><td>0.329770119</td><td>0.59105711</td></tr>
<tr><td>9</td><td>r</td><td>0</td><td>0.04265665</td><td>0.014252784</td><td>0.021987458</td><td>0.08114372</td></tr>
<tr><td>9</td><td>r</td><td>1</td><td>0.05023886</td><td>0.01324723</td><td>0.029782993</td><td>0.08353476</td></tr>
<tr><td>9</td><td>r</td><td>2</td><td>0.03066049</td><td>0.004743868</td><td>0.022610114</td><td>0.04145564</td></tr>
<tr><td>9</td><td>S</td><td>0</td><td>0.707582</td><td>0.081364409</td><td>0.528201565</td><td>0.83948651</td></tr>
<tr><td>9</td><td>S</td><td>1</td><td>0.64823762</td><td>0.080717654</td><td>0.479384385</td><td>0.78669409</td></tr>
<tr><td>9</td><td>S</td><td>2</td><td>0.39731762</td><td>0.059122183</td><td>0.288930965</td><td>0.51681193</td></tr>
<tr><td>10</td><td>r</td><td>0</td><td>0.05073267</td><td>0.021461764</td><td>0.021826016</td><td>0.11348213</td></tr>
<tr><td>10</td><td>r</td><td>1</td><td>0.17151312</td><td>0.137100846</td><td>0.03029422</td><td>0.57838648</td></tr>
<tr><td>10</td><td>r</td><td>2</td><td>0.02697447</td><td>0.004202228</td><td>0.019853469</td><td>0.03655437</td></tr>
<tr><td>10</td><td>S</td><td>0</td><td>0.77293144</td><td>0.082961396</td><td>0.574066594</td><td>0.89580086</td></tr>
<tr><td>10</td><td>S</td><td>1</td><td>0.87792697</td><td>0.096004392</td><td>0.554079661</td><td>0.97654002</td></tr>
<tr><td>10</td><td>S</td><td>2</td><td>0.34236516</td><td>0.052432243</td><td>0.248018532</td><td>0.45107314</td></tr>
<tr><td>11</td><td>r</td><td>0</td><td>0.1989052</td><td>0.231482706</td><td>0.014195608</td><td>0.81064757</td></tr>
<tr><td>11</td><td>r</td><td>1</td><td>0.10338734</td><td>0.062459041</td><td>0.029864496</td><td>0.30163641</td></tr>
<tr><td>11</td><td>r</td><td>2</td><td>0.0247491</td><td>0.004616067</td><td>0.017145061</td><td>0.03560345</td></tr>
<tr><td>11</td><td>S</td><td>0</td><td>0.94824862</td><td>0.05886078</td><td>0.635814879</td><td>0.99482685</td></tr>
<tr><td>11</td><td>S</td><td>1</td><td>0.81253265</td><td>0.109048897</td><td>0.515839567</td><td>0.9463293</td></tr>
<tr><td>11</td><td>S</td><td>2</td><td>0.37768417</td><td>0.06762882</td><td>0.256669914</td><td>0.51613703</td></tr>
<tr><td>12</td><td>r</td><td>0</td><td>0.02033283</td><td>0.00461035</td><td>0.013014147</td><td>0.03163534</td></tr>
<tr><td>12</td><td>r</td><td>1</td><td>0.08773973</td><td>0.050815043</td><td>0.026965166</td><td>0.25025984</td></tr>
<tr><td>12</td><td>r</td><td>2</td><td>0.02542478</td><td>0.003780837</td><td>0.018977401</td><td>0.03398669</td></tr>
<tr><td>12</td><td>S</td><td>0</td><td>0.60707478</td><td>0.070699176</td><td>0.463592049</td><td>0.734184</td></tr>
<tr><td>12</td><td>S</td><td>1</td><td>0.80503904</td><td>0.109492339</td><td>0.512686168</td><td>0.94188324</td></tr>
<tr><td>12</td><td>S</td><td>2</td><td>0.27134388</td><td>0.052237016</td><td>0.181584294</td><td>0.38462109</td></tr>
<tr><td>13</td><td>r</td><td>0</td><td>0.02558374</td><td>0.006999428</td><td>0.014917431</td><td>0.04353961</td></tr>
<tr><td>13</td><td>r</td><td>1</td><td>0.05345407</td><td>0.02098894</td><td>0.024433586</td><td>0.11295223</td></tr>
<tr><td>13</td><td>r</td><td>2</td><td>0.03335003</td><td>0.006012826</td><td>0.023377032</td><td>0.04737129</td></tr>
<tr><td>13</td><td>S</td><td>0</td><td>0.61494509</td><td>0.079824115</td><td>0.451999814</td><td>0.75563361</td></tr>
<tr><td>13</td><td>S</td><td>1</td><td>0.73244904</td><td>0.098054781</td><td>0.506592869</td><td>0.8795096</td></tr>
<tr><td>13</td><td>S</td><td>2</td><td>0.43089938</td><td>0.071966327</td><td>0.29872331</td><td>0.57371374</td></tr>
<tr><td>14</td><td>r</td><td>0</td><td>0.03087169</td><td>0.01076987</td><td>0.015487663</td><td>0.06059626</td></tr>
<tr><td>14</td><td>r</td><td>1</td><td>0.05566887</td><td>0.020722943</td><td>0.026501774</td><td>0.11320363</td></tr>
<tr><td>14</td><td>r</td><td>2</td><td>0.02717595</td><td>0.004000389</td><td>0.020343333</td><td>0.03621857</td></tr>
<tr><td>14</td><td>S</td><td>0</td><td>0.68386425</td><td>0.090436343</td><td>0.487926794</td><td>0.8308239</td></tr>
<tr><td>14</td><td>S</td><td>1</td><td>0.71968005</td><td>0.09830256</td><td>0.496955589</td><td>0.86965684</td></tr>
<tr><td>14</td><td>S</td><td>2</td><td>0.30960179</td><td>0.056527417</td><td>0.210765315</td><td>0.42956045</td></tr>
<tr><td>15</td><td>r</td><td>0</td><td>0.02077447</td><td>0.004884806</td><td>0.013077748</td><td>0.03285022</td></tr>
<tr><td>15</td><td>r</td><td>1</td><td>0.03526933</td><td>0.007900198</td><td>0.02266698</td><td>0.05448769</td></tr>
<tr><td>15</td><td>r</td><td>2</td><td>0.03190254</td><td>0.006003522</td><td>0.022017785</td><td>0.04601619</td></tr>
<tr><td>15</td><td>S</td><td>0</td><td>0.54620346</td><td>0.072905431</td><td>0.403438388</td><td>0.68175394</td></tr>
<tr><td>15</td><td>S</td><td>1</td><td>0.5701852</td><td>0.079171163</td><td>0.413243922</td><td>0.71418244</td></tr>
<tr><td>15</td><td>S</td><td>2</td><td>0.46865988</td><td>0.074190549</td><td>0.32971526</td><td>0.61264002</td></tr>
<tr><td>16</td><td>r</td><td>0</td><td>0.03164703</td><td>0.009255488</td><td>0.017759642</td><td>0.05577718</td></tr>
<tr><td>16</td><td>r</td><td>1</td><td>0.03287072</td><td>0.007155581</td><td>0.021396038</td><td>0.05018369</td></tr>
<tr><td>16</td><td>r</td><td>2</td><td>0.02552615</td><td>0.004065618</td><td>0.018659835</td><td>0.03482942</td></tr>
<tr><td>16</td><td>S</td><td>0</td><td>0.64205543</td><td>0.08118908</td><td>0.472997105</td><td>0.78188919</td></tr>
<tr><td>16</td><td>S</td><td>1</td><td>0.57100256</td><td>0.076497083</td><td>0.419180781</td><td>0.710543</td></tr>
<tr><td>16</td><td>S</td><td>2</td><td>0.36375624</td><td>0.062934825</td><td>0.251226708</td><td>0.4934716</td></tr>
<tr><td>17</td><td>r</td><td>0</td><td>0.05370209</td><td>0.026230102</td><td>0.020217718</td><td>0.13500173</td></tr>
<tr><td>17</td><td>r</td><td>1</td><td>0.02888923</td><td>0.007741817</td><td>0.017025959</td><td>0.04860975</td></tr>
<tr><td>17</td><td>r</td><td>2</td><td>0.02895547</td><td>0.005752164</td><td>0.019577769</td><td>0.04262974</td></tr>
<tr><td>17</td><td>S</td><td>0</td><td>0.78285623</td><td>0.094849449</td><td>0.547055793</td><td>0.91497854</td></tr>
<tr><td>17</td><td>S</td><td>1</td><td>0.62265597</td><td>0.086736294</td><td>0.444549586</td><td>0.77283543</td></tr>
<tr><td>17</td><td>S</td><td>2</td><td>0.43893522</td><td>0.082581082</td><td>0.288491173</td><td>0.60150845</td></tr>
<tr><td>18</td><td>r</td><td>0</td><td>0.10711839</td><td>0.112179204</td><td>0.011898551</td><td>0.54446474</td></tr>
<tr><td>18</td><td>r</td><td>1</td><td>0.05727955</td><td>0.03980769</td><td>0.014122885</td><td>0.20490469</td></tr>
<tr><td>18</td><td>r</td><td>2</td><td>0.0293295</td><td>0.005881378</td><td>0.01975679</td><td>0.0433354</td></tr>
<tr><td>18</td><td>S</td><td>0</td><td>0.9055813</td><td>0.09615592</td><td>0.514161224</td><td>0.98862633</td></tr>
<tr><td>18</td><td>S</td><td>1</td><td>0.79637596</td><td>0.137296015</td><td>0.426617782</td><td>0.95361377</td></tr>
<tr><td>18</td><td>S</td><td>2</td><td>0.43112328</td><td>0.087089216</td><td>0.274231959</td><td>0.60317616</td></tr>
<tr><td>19</td><td>r</td><td>0</td><td>0.01626999</td><td>0.00511359</td><td>0.008764547</td><td>0.03000809</td></tr>
<tr><td>19</td><td>r</td><td>1</td><td>0.0508963</td><td>0.039885569</td><td>0.010518183</td><td>0.21292566</td></tr>
<tr><td>19</td><td>r</td><td>2</td><td>0.0326756</td><td>0.010151299</td><td>0.017681693</td><td>0.05961258</td></tr>
<tr><td>19</td><td>S</td><td>0</td><td>0.57134542</td><td>0.106226301</td><td>0.362904299</td><td>0.75721406</td></tr>
<tr><td>19</td><td>S</td><td>1</td><td>0.7999377</td><td>0.153033934</td><td>0.380288744</td><td>0.96303552</td></tr>
<tr><td>19</td><td>S</td><td>2</td><td>0.56178688</td><td>0.118651594</td><td>0.332647143</td><td>0.76728986</td></tr>
<tr><td>20</td><td>r</td><td>0</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td></tr>
<tr><td>20</td><td>r</td><td>1</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td></tr>
<tr><td>20</td><td>r</td><td>2</td><td>0.02919039</td><td>0.005713846</td><td>0.019850317</td><td>0.04273358</td></tr>
<tr><td>20</td><td>S</td><td>0</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td></tr>
<tr><td>20</td><td>S</td><td>1</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td></tr>
<tr><td>20</td><td>S</td><td>2</td><td>0.40928921</td><td>0.092661991</td><td>0.246364031</td><td>0.59490742</td></tr>
<tr style="border-bottom:1px solid black"><td colspan="100%"></td></tr>
<tr><td>20</td><td>f</td><td>0</td><td>0.008805653</td><td>0.001272182</td><td>0.006631966</td><td>0.01168341</td></tr>
<tr><td>20</td><td>f</td><td>1</td><td>0.008805653</td><td>0.001443674</td><td>0.011901662</td><td>0.01759484</td></tr>
</table>
</details>

<details>
<summary>M<sub>comb</sub> (GLM) [Seber]</summary>
<table style="margin: 0 auto; border-collapse: collapse; width: auto;">
<tr><th>time</th><th>Param</th><th>Age</th><th>Estim</th><th>StdErr</th><th>Lower</th><th>Upper</th></tr>
<tr><td>1</td><td>r</td><td>0</td><td>0.05492224</td><td>0.016036489</td><td>0.03074208</td><td>0.09623298</td></tr>
<tr><td>1</td><td>r</td><td>1</td><td>0.05236602</td><td>0.0077574</td><td>0.039086563</td><td>0.06982926</td></tr>
<tr><td>1</td><td>r</td><td>2</td><td>0.02869425</td><td>0.006940743</td><td>0.017809602</td><td>0.04592021</td></tr>
<tr><td>1</td><td>S</td><td>0</td><td>0.62428405</td><td>0.093481262</td><td>0.432076097</td><td>0.783966</td></tr>
<tr><td>1</td><td>S</td><td>1</td><td>0.48009339</td><td>0.061192979</td><td>0.363505081</td><td>0.59889092</td></tr>
<tr><td>1</td><td>S</td><td>2</td><td>0.48306593</td><td>0.098828418</td><td>0.300805143</td><td>0.66994634</td></tr>
<tr><td>2</td><td>r</td><td>0</td><td>0.09343643</td><td>0.054934478</td><td>0.028099545</td><td>0.26869381</td></tr>
<tr><td>2</td><td>r</td><td>1</td><td>0.05209752</td><td>0.011258798</td><td>0.033961042</td><td>0.07912623</td></tr>
<tr><td>2</td><td>r</td><td>2</td><td>0.04734273</td><td>0.011685586</td><td>0.029038346</td><td>0.07627889</td></tr>
<tr><td>2</td><td>S</td><td>0</td><td>0.8048466</td><td>0.106345138</td><td>0.522441008</td><td>0.93956833</td></tr>
<tr><td>2</td><td>S</td><td>1</td><td>0.59542488</td><td>0.077652819</td><td>0.438962655</td><td>0.73463134</td></tr>
<tr><td>2</td><td>S</td><td>2</td><td>0.6449402</td><td>0.101105835</td><td>0.433271069</td><td>0.81187814</td></tr>
<tr><td>3</td><td>r</td><td>0</td><td>0.04081756</td><td>0.01541993</td><td>0.019285638</td><td>0.08432238</td></tr>
<tr><td>3</td><td>r</td><td>1</td><td>0.03744791</td><td>0.00693014</td><td>0.025996118</td><td>0.05366647</td></tr>
<tr><td>3</td><td>r</td><td>2</td><td>0.02943397</td><td>0.004551404</td><td>0.021710744</td><td>0.03979284</td></tr>
<tr><td>3</td><td>S</td><td>0</td><td>0.70900407</td><td>0.089804548</td><td>0.509352665</td><td>0.85115503</td></tr>
<tr><td>3</td><td>S</td><td>1</td><td>0.52844685</td><td>0.06791782</td><td>0.396446289</td><td>0.65658562</td></tr>
<tr><td>3</td><td>S</td><td>2</td><td>0.42054814</td><td>0.075129796</td><td>0.283980987</td><td>0.57046588</td></tr>
<tr><td>4</td><td>r</td><td>0</td><td>0.05235714</td><td>0.018160924</td><td>0.026254584</td><td>0.10170067</td></tr>
<tr><td>4</td><td>r</td><td>1</td><td>0.03987568</td><td>0.007652673</td><td>0.027303204</td><td>0.05789292</td></tr>
<tr><td>4</td><td>r</td><td>2</td><td>0.02357181</td><td>0.003845064</td><td>0.017102409</td><td>0.03240774</td></tr>
<tr><td>4</td><td>S</td><td>0</td><td>0.70793221</td><td>0.085657459</td><td>0.518337097</td><td>0.84518746</td></tr>
<tr><td>4</td><td>S</td><td>1</td><td>0.56096449</td><td>0.07054977</td><td>0.42155825</td><td>0.69137054</td></tr>
<tr><td>4</td><td>S</td><td>2</td><td>0.38255982</td><td>0.070166995</td><td>0.257132285</td><td>0.52586016</td></tr>
<tr><td>5</td><td>r</td><td>0</td><td>0.02720135</td><td>0.005308015</td><td>0.018522457</td><td>0.03978201</td></tr>
<tr><td>5</td><td>r</td><td>1</td><td>0.02479941</td><td>0.003472084</td><td>0.018831471</td><td>0.03259584</td></tr>
<tr><td>5</td><td>r</td><td>2</td><td>0.04383221</td><td>0.006035622</td><td>0.033413031</td><td>0.05730777</td></tr>
<tr><td>5</td><td>S</td><td>0</td><td>0.51149657</td><td>0.059065053</td><td>0.397158089</td><td>0.62464455</td></tr>
<tr><td>5</td><td>S</td><td>1</td><td>0.42144612</td><td>0.047528763</td><td>0.332057165</td><td>0.51629822</td></tr>
<tr><td>5</td><td>S</td><td>2</td><td>0.42172688</td><td>0.072959687</td><td>0.288626853</td><td>0.56726123</td></tr>
<tr><td>6</td><td>r</td><td>0</td><td>0.11791086</td><td>0.068586691</td><td>0.035404788</td><td>0.32742229</td></tr>
<tr><td>6</td><td>r</td><td>1</td><td>0.03574597</td><td>0.006731234</td><td>0.024658105</td><td>0.05155609</td></tr>
<tr><td>6</td><td>r</td><td>2</td><td>0.03043635</td><td>0.00422271</td><td>0.023165046</td><td>0.03989684</td></tr>
<tr><td>6</td><td>S</td><td>0</td><td>0.83394093</td><td>0.089553812</td><td>0.585728707</td><td>0.94691436</td></tr>
<tr><td>6</td><td>S</td><td>1</td><td>0.56731781</td><td>0.065686506</td><td>0.436944181</td><td>0.68899085</td></tr>
<tr><td>6</td><td>S</td><td>2</td><td>0.38025293</td><td>0.065080443</td><td>0.26313224</td><td>0.51319747</td></tr>
<tr><td>7</td><td>r</td><td>0</td><td>0.07114466</td><td>0.036475095</td><td>0.025306163</td><td>0.18431229</td></tr>
<tr><td>7</td><td>r</td><td>1</td><td>0.03860844</td><td>0.009120761</td><td>0.024208831</td><td>0.06103729</td></tr>
<tr><td>7</td><td>r</td><td>2</td><td>0.02516855</td><td>0.003962299</td><td>0.018465739</td><td>0.03421957</td></tr>
<tr><td>7</td><td>S</td><td>0</td><td>0.80433062</td><td>0.090541647</td><td>0.571017807</td><td>0.9269776</td></tr>
<tr><td>7</td><td>S</td><td>1</td><td>0.61553083</td><td>0.077323601</td><td>0.457655011</td><td>0.752321</td></tr>
<tr><td>7</td><td>S</td><td>2</td><td>0.32294065</td><td>0.068260091</td><td>0.205514108</td><td>0.46794398</td></tr>
<tr><td>8</td><td>r</td><td>0</td><td>0.05381334</td><td>0.025369644</td><td>0.020969598</td><td>0.13120482</td></tr>
<tr><td>8</td><td>r</td><td>1</td><td>0.04431339</td><td>0.013272342</td><td>0.02447307</td><td>0.07893675</td></tr>
<tr><td>8</td><td>r</td><td>2</td><td>0.02972108</td><td>0.005933523</td><td>0.0200552</td><td>0.04383717</td></tr>
<tr><td>8</td><td>S</td><td>0</td><td>0.78435842</td><td>0.089948378</td><td>0.561911978</td><td>0.91161951</td></tr>
<tr><td>8</td><td>S</td><td>1</td><td>0.67850331</td><td>0.086386522</td><td>0.492676072</td><td>0.82099481</td></tr>
<tr><td>8</td><td>S</td><td>2</td><td>0.49037521</td><td>0.088870424</td><td>0.323987571</td><td>0.65892301</td></tr>
<tr><td>9</td><td>r</td><td>0</td><td>0.04243523</td><td>0.014160687</td><td>0.021892962</td><td>0.08066282</td></tr>
<tr><td>9</td><td>r</td><td>1</td><td>0.04926518</td><td>0.011837249</td><td>0.03060935</td><td>0.07837214</td></tr>
<tr><td>9</td><td>r</td><td>2</td><td>0.02916883</td><td>0.00549106</td><td>0.020131953</td><td>0.04208796</td></tr>
<tr><td>9</td><td>S</td><td>0</td><td>0.70650189</td><td>0.081357675</td><td>0.527332136</td><td>0.83854906</td></tr>
<tr><td>9</td><td>S</td><td>1</td><td>0.61851904</td><td>0.081235243</td><td>0.452262779</td><td>0.76098118</td></tr>
<tr><td>9</td><td>S</td><td>2</td><td>0.47301788</td><td>0.089970231</td><td>0.30672819</td><td>0.64551749</td></tr>
<tr><td>10</td><td>r</td><td>0</td><td>0.04997228</td><td>0.02097729</td><td>0.021645661</td><td>0.11115684</td></tr>
<tr><td>10</td><td>r</td><td>1</td><td>0.22211152</td><td>0.261577115</td><td>0.014475106</td><td>0.84734623</td></tr>
<tr><td>10</td><td>r</td><td>2</td><td>0.02882634</td><td>0.004195787</td><td>0.021647728</td><td>0.03829228</td></tr>
<tr><td>10</td><td>S</td><td>0</td><td>0.76995249</td><td>0.083513594</td><td>0.570505946</td><td>0.89399085</td></tr>
<tr><td>10</td><td>S</td><td>1</td><td>0.91036574</td><td>0.105296122</td><td>0.447429018</td><td>0.99221145</td></tr>
<tr><td>10</td><td>S</td><td>2</td><td>0.28208999</td><td>0.060606803</td><td>0.17936023</td><td>0.4139771</td></tr>
<tr><td>11</td><td>r</td><td>0</td><td>0.20046918</td><td>0.262951814</td><td>0.00996293</td><td>0.86201672</td></tr>
<tr><td>11</td><td>r</td><td>1</td><td>0.09268405</td><td>0.050415942</td><td>0.030579892</td><td>0.24857378</td></tr>
<tr><td>11</td><td>r</td><td>2</td><td>0.0223854</td><td>0.005443107</td><td>0.013868056</td><td>0.03594316</td></tr>
<tr><td>11</td><td>S</td><td>0</td><td>0.9484709</td><td>0.066060996</td><td>0.565482742</td><td>0.99617348</td></tr>
<tr><td>11</td><td>S</td><td>1</td><td>0.77867466</td><td>0.117831711</td><td>0.479481154</td><td>0.93073537</td></tr>
<tr><td>11</td><td>S</td><td>2</td><td>0.44864611</td><td>0.109809871</td><td>0.254220057</td><td>0.66014611</td></tr>
<tr><td>12</td><td>r</td><td>0</td><td>0.02044916</td><td>0.004811473</td><td>0.012869431</td><td>0.03234683</td></tr>
<tr><td>12</td><td>r</td><td>1</td><td>0.08198354</td><td>0.042624948</td><td>0.028588447</td><td>0.21321631</td></tr>
<tr><td>12</td><td>r</td><td>2</td><td>0.02456545</td><td>0.004034318</td><td>0.017783616</td><td>0.03384444</td></tr>
<tr><td>12</td><td>S</td><td>0</td><td>0.60804848</td><td>0.072675287</td><td>0.460440746</td><td>0.73823235</td></tr>
<tr><td>12</td><td>S</td><td>1</td><td>0.78482737</td><td>0.109352443</td><td>0.506210341</td><td>0.92845556</td></tr>
<tr><td>12</td><td>S</td><td>2</td><td>0.30863067</td><td>0.076413524</td><td>0.181171308</td><td>0.47386738</td></tr>
<tr><td>13</td><td>r</td><td>0</td><td>0.0256717</td><td>0.007141913</td><td>0.014832347</td><td>0.04407796</td></tr>
<tr><td>13</td><td>r</td><td>1</td><td>0.05147137</td><td>0.018661952</td><td>0.02501162</td><td>0.10296667</td></tr>
<tr><td>13</td><td>r</td><td>2</td><td>0.03361145</td><td>0.007336174</td><td>0.021851844</td><td>0.05136718</td></tr>
<tr><td>13</td><td>S</td><td>0</td><td>0.61563494</td><td>0.081391894</td><td>0.449396444</td><td>0.75863902</td></tr>
<tr><td>13</td><td>S</td><td>1</td><td>0.70625226</td><td>0.100928853</td><td>0.480938061</td><td>0.86185545</td></tr>
<tr><td>13</td><td>S</td><td>2</td><td>0.51761802</td><td>0.109460361</td><td>0.312440404</td><td>0.71702199</td></tr>
<tr><td>14</td><td>r</td><td>0</td><td>0.03077936</td><td>0.011010592</td><td>0.015171362</td><td>0.06144275</td></tr>
<tr><td>14</td><td>r</td><td>1</td><td>0.05353136</td><td>0.018332459</td><td>0.027075966</td><td>0.1030967</td></tr>
<tr><td>14</td><td>r</td><td>2</td><td>0.02609422</td><td>0.004401454</td><td>0.01872373</td><td>0.03625887</td></tr>
<tr><td>14</td><td>S</td><td>0</td><td>0.68304694</td><td>0.092864188</td><td>0.481776937</td><td>0.83320919</td></tr>
<tr><td>14</td><td>S</td><td>1</td><td>0.6952237</td><td>0.099323573</td><td>0.476490951</td><td>0.85112054</td></tr>
<tr><td>14</td><td>S</td><td>2</td><td>0.37731807</td><td>0.087861606</td><td>0.225497971</td><td>0.55774482</td></tr>
<tr><td>15</td><td>r</td><td>0</td><td>0.02079857</td><td>0.004983639</td><td>0.012977536</td><td>0.03317461</td></tr>
<tr><td>15</td><td>r</td><td>1</td><td>0.03478944</td><td>0.007163817</td><td>0.023176179</td><td>0.05191269</td></tr>
<tr><td>15</td><td>r</td><td>2</td><td>0.03207052</td><td>0.007759407</td><td>0.019895749</td><td>0.0513054</td></tr>
<tr><td>15</td><td>S</td><td>0</td><td>0.54673999</td><td>0.074153753</td><td>0.401556694</td><td>0.68438508</td></tr>
<tr><td>15</td><td>S</td><td>1</td><td>0.53881631</td><td>0.079393708</td><td>0.384464184</td><td>0.68606773</td></tr>
<tr><td>15</td><td>S</td><td>2</td><td>0.57237976</td><td>0.120403463</td><td>0.337914806</td><td>0.77829099</td></tr>
<tr><td>16</td><td>r</td><td>0</td><td>0.03180069</td><td>0.009450585</td><td>0.017678701</td><td>0.05655405</td></tr>
<tr><td>16</td><td>r</td><td>1</td><td>0.03294219</td><td>0.006691717</td><td>0.022070155</td><td>0.0489021</td></tr>
<tr><td>16</td><td>r</td><td>2</td><td>0.02451073</td><td>0.004815097</td><td>0.016650208</td><td>0.03594652</td></tr>
<tr><td>16</td><td>S</td><td>0</td><td>0.64402813</td><td>0.08229323</td><td>0.47236147</td><td>0.78523663</td></tr>
<tr><td>16</td><td>S</td><td>1</td><td>0.54381996</td><td>0.077219621</td><td>0.393086381</td><td>0.68693277</td></tr>
<tr><td>16</td><td>S</td><td>2</td><td>0.46271453</td><td>0.10282917</td><td>0.276856563</td><td>0.65954551</td></tr>
<tr><td>17</td><td>r</td><td>0</td><td>0.05264515</td><td>0.025541439</td><td>0.019960049</td><td>0.13166252</td></tr>
<tr><td>17</td><td>r</td><td>1</td><td>0.02907819</td><td>0.007779537</td><td>0.017152037</td><td>0.0488844</td></tr>
<tr><td>17</td><td>r</td><td>2</td><td>0.02894203</td><td>0.006077836</td><td>0.019134175</td><td>0.04355399</td></tr>
<tr><td>17</td><td>S</td><td>0</td><td>0.77947579</td><td>0.095401495</td><td>0.543590531</td><td>0.9129678</td></tr>
<tr><td>17</td><td>S</td><td>1</td><td>0.61966961</td><td>0.090077865</td><td>0.435123674</td><td>0.77508825</td></tr>
<tr><td>17</td><td>S</td><td>2</td><td>0.46339372</td><td>0.115607094</td><td>0.257704938</td><td>0.68234215</td></tr>
<tr><td>18</td><td>r</td><td>0</td><td>0.10820172</td><td>0.117213195</td><td>0.011095136</td><td>0.56748711</td></tr>
<tr><td>18</td><td>r</td><td>1</td><td>0.05953673</td><td>0.043638385</td><td>0.013555189</td><td>0.22579304</td></tr>
<tr><td>18</td><td>r</td><td>2</td><td>0.02860714</td><td>0.005365708</td><td>0.019771791</td><td>0.04122467</td></tr>
<tr><td>18</td><td>S</td><td>0</td><td>0.90677006</td><td>0.098245094</td><td>0.499256607</td><td>0.9895703</td></tr>
<tr><td>18</td><td>S</td><td>1</td><td>0.80755355</td><td>0.139134786</td><td>0.420544244</td><td>0.96041528</td></tr>
<tr><td>18</td><td>S</td><td>2</td><td>0.38935185</td><td>0.120107914</td><td>0.19151864</td><td>0.6318351</td></tr>
<tr><td>19</td><td>r</td><td>0</td><td>0.0162449</td><td>0.005313565</td><td>0.008532671</td><td>0.03071187</td></tr>
<tr><td>19</td><td>r</td><td>1</td><td>0.04149431</td><td>0.027488792</td><td>0.011047157</td><td>0.14366655</td></tr>
<tr><td>19</td><td>r</td><td>2</td><td>0.0445446</td><td>0.033863875</td><td>0.00970652</td><td>0.18150404</td></tr>
<tr><td>19</td><td>S</td><td>0</td><td>0.56998042</td><td>0.112632643</td><td>0.350028898</td><td>0.76538888</td></tr>
<tr><td>19</td><td>S</td><td>1</td><td>0.73923466</td><td>0.169607776</td><td>0.335696619</td><td>0.94083959</td></tr>
<tr><td>19</td><td>S</td><td>2</td><td>0.75001282</td><td>0.237846796</td><td>0.199775803</td><td>0.9730134</td></tr>
<tr><td>20</td><td>r</td><td>0</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td></tr>
<tr><td>20</td><td>r</td><td>1</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td></tr>
<tr><td>20</td><td>r</td><td>2</td><td>0.03445507</td><td>0.052246168</td><td>0.001640435</td><td>0.43661267</td></tr>
<tr><td>20</td><td>S</td><td>0</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td></tr>
<tr><td>20</td><td>S</td><td>1</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td></tr>
<tr><td>20</td><td>S</td><td>2</td><td>0.56890789</td><td>1.127204802</td><td>0.000161477</td><td>0.99990728</td></tr>
<tr style="border-bottom:1px solid black"><td colspan="100%"></td></tr>
<tr><td>20</td><td>f</td><td>0</td><td>0.008861339</td><td>0.001375695</td><td>0.006534128</td><td>0.0120074</td></tr>
<tr><td>20</td><td>f</td><td>1</td><td>0.008861339</td><td>0.00144681</td><td>0.011910795</td><td>0.01761642</td></tr>
</table>
</details>

Data frames containing the Bayesian estimates, standard errors, 95% credible intervals, and important statistics are below

<details>
<summary>M<sub>sep</sub> (RW1) excl. tau</summary>
<table style="margin: 0 auto; border-collapse: collapse; width: auto;">
<tr><th>Time</th><th>Param</th><th>Age</th><th>Mean</th><th>Median</th><th>StdErr</th><th>Lower</th><th>Upper</th><th>ESS</th><th>$\hat{r}$</th></tr>
<tr><td>1</td><td>S</td><td>2</td><td>0.39921001</td><td>0.3983155</td><td>0.02557878</td><td>0.348475</td><td>0.452157</td><td>16017</td><td>1.0001011</td></tr>
<tr><td>1</td><td>S</td><td>1</td><td>0.55916828</td><td>0.559323</td><td>0.039392001</td><td>0.484113</td><td>0.637799</td><td>16464</td><td>1.0011265</td></tr>
<tr><td>1</td><td>S</td><td>0</td><td>0.6929505</td><td>0.695427</td><td>0.028604061</td><td>0.63528</td><td>0.747096</td><td>51732</td><td>1.0000224</td></tr>
<tr><td>1</td><td>r</td><td>2</td><td>0.03342943</td><td>0.03296785</td><td>0.003110479</td><td>0.0281698</td><td>0.0397612</td><td>13984</td><td>1.0000026</td></tr>
<tr><td>1</td><td>r</td><td>1</td><td>0.0550541</td><td>0.05450495</td><td>0.006538902</td><td>0.0430037</td><td>0.0681436</td><td>21131</td><td>1.0005953</td></tr>
<tr><td>1</td><td>r</td><td>0</td><td>0.05981276</td><td>0.05902545</td><td>0.007401549</td><td>0.0462097</td><td>0.0745709</td><td>51589</td><td>1.0000432</td></tr>
<tr><td>2</td><td>S</td><td>2</td><td>0.40429107</td><td>0.4021575</td><td>0.02397693</td><td>0.3585</td><td>0.453288</td><td>12437</td><td>1.0001326</td></tr>
<tr><td>2</td><td>S</td><td>1</td><td>0.57492556</td><td>0.5747635</td><td>0.033161591</td><td>0.511957</td><td>0.641737</td><td>16667</td><td>1.0008401</td></tr>
<tr><td>2</td><td>S</td><td>0</td><td>0.7028249</td><td>0.7035125</td><td>0.020866857</td><td>0.660469</td><td>0.742886</td><td>53061</td><td>1.0000164</td></tr>
<tr><td>2</td><td>r</td><td>2</td><td>0.03286775</td><td>0.03256035</td><td>0.002554107</td><td>0.0283171</td><td>0.0379754</td><td>13182</td><td>0.9999895</td></tr>
<tr><td>2</td><td>r</td><td>1</td><td>0.04936427</td><td>0.0489572</td><td>0.005136421</td><td>0.0394141</td><td>0.0593864</td><td>18851</td><td>1.0004812</td></tr>
<tr><td>2</td><td>r</td><td>0</td><td>0.0556857</td><td>0.05533325</td><td>0.005071793</td><td>0.0463612</td><td>0.0660861</td><td>46371</td><td>1.0000929</td></tr>
<tr><td>3</td><td>S</td><td>2</td><td>0.39846673</td><td>0.3977985</td><td>0.021676294</td><td>0.356441</td><td>0.442908</td><td>12523</td><td>1.0001904</td></tr>
<tr><td>3</td><td>S</td><td>1</td><td>0.57208541</td><td>0.57242</td><td>0.034590689</td><td>0.504557</td><td>0.639502</td><td>15868</td><td>1.0005194</td></tr>
<tr><td>3</td><td>S</td><td>0</td><td>0.71826624</td><td>0.7177545</td><td>0.017457947</td><td>0.683327</td><td>0.752682</td><td>52700</td><td>1.0000671</td></tr>
<tr><td>3</td><td>r</td><td>2</td><td>0.03178833</td><td>0.03165355</td><td>0.002014118</td><td>0.0279954</td><td>0.0359041</td><td>15826</td><td>1.0002621</td></tr>
<tr><td>3</td><td>r</td><td>1</td><td>0.04293594</td><td>0.04269375</td><td>0.004560394</td><td>0.0341042</td><td>0.0519023</td><td>18228</td><td>1.0005321</td></tr>
<tr><td>3</td><td>r</td><td>0</td><td>0.05043196</td><td>0.05032405</td><td>0.003926208</td><td>0.0428605</td><td>0.0582423</td><td>43468</td><td>1.0003508</td></tr>
<tr><td>4</td><td>S</td><td>2</td><td>0.39408343</td><td>0.3944315</td><td>0.021274676</td><td>0.351508</td><td>0.436676</td><td>11556</td><td>1.000181</td></tr>
<tr><td>4</td><td>S</td><td>1</td><td>0.57675547</td><td>0.577543</td><td>0.034640771</td><td>0.508165</td><td>0.643387</td><td>16556</td><td>1.0006321</td></tr>
<tr><td>4</td><td>S</td><td>0</td><td>0.70477188</td><td>0.705702</td><td>0.016719524</td><td>0.670897</td><td>0.736602</td><td>47711</td><td>1.0000828</td></tr>
<tr><td>4</td><td>r</td><td>2</td><td>0.03131276</td><td>0.03123745</td><td>0.001891397</td><td>0.0276856</td><td>0.0351563</td><td>17164</td><td>1.0000793</td></tr>
<tr><td>4</td><td>r</td><td>1</td><td>0.04019104</td><td>0.0400177</td><td>0.004262773</td><td>0.0320337</td><td>0.0485312</td><td>18261</td><td>1.0004476</td></tr>
<tr><td>4</td><td>r</td><td>0</td><td>0.05131637</td><td>0.0511807</td><td>0.003599616</td><td>0.0442586</td><td>0.0584666</td><td>35532</td><td>1.0001624</td></tr>
<tr><td>5</td><td>S</td><td>2</td><td>0.39110395</td><td>0.392033</td><td>0.020833618</td><td>0.348891</td><td>0.432104</td><td>10931</td><td>1.000348</td></tr>
<tr><td>5</td><td>S</td><td>1</td><td>0.59416619</td><td>0.5949545</td><td>0.032044125</td><td>0.531636</td><td>0.656895</td><td>18891</td><td>1.0005623</td></tr>
<tr><td>5</td><td>S</td><td>0</td><td>0.70959948</td><td>0.710466</td><td>0.015961347</td><td>0.677114</td><td>0.740194</td><td>49504</td><td>0.9999918</td></tr>
<tr><td>5</td><td>r</td><td>2</td><td>0.03206715</td><td>0.03179605</td><td>0.00215086</td><td>0.02829</td><td>0.0365307</td><td>13992</td><td>1.0000316</td></tr>
<tr><td>5</td><td>r</td><td>1</td><td>0.03927128</td><td>0.03910575</td><td>0.004123003</td><td>0.0315044</td><td>0.0475799</td><td>21091</td><td>1.0004363</td></tr>
<tr><td>5</td><td>r</td><td>0</td><td>0.04855481</td><td>0.0485466</td><td>0.003390012</td><td>0.0419787</td><td>0.0553283</td><td>35583</td><td>1.0000435</td></tr>
<tr><td>6</td><td>S</td><td>2</td><td>0.39273299</td><td>0.393303</td><td>0.020397962</td><td>0.350004</td><td>0.431859</td><td>11805</td><td>1.0005687</td></tr>
<tr><td>6</td><td>S</td><td>1</td><td>0.61509284</td><td>0.615372</td><td>0.029722883</td><td>0.555777</td><td>0.673029</td><td>22055</td><td>1.0004305</td></tr>
<tr><td>6</td><td>S</td><td>0</td><td>0.71307552</td><td>0.7137525</td><td>0.015147522</td><td>0.681848</td><td>0.741534</td><td>45181</td><td>0.9999922</td></tr>
<tr><td>6</td><td>r</td><td>2</td><td>0.03108105</td><td>0.0309756</td><td>0.001820232</td><td>0.0276312</td><td>0.0347893</td><td>16431</td><td>1.0002322</td></tr>
<tr><td>6</td><td>r</td><td>1</td><td>0.04033381</td><td>0.04015085</td><td>0.00412172</td><td>0.0323707</td><td>0.0484485</td><td>21828</td><td>1.0002823</td></tr>
<tr><td>6</td><td>r</td><td>0</td><td>0.05076062</td><td>0.05055755</td><td>0.00348014</td><td>0.0442177</td><td>0.0577905</td><td>34337</td><td>1.0002082</td></tr>
<tr><td>7</td><td>S</td><td>2</td><td>0.39276748</td><td>0.3934935</td><td>0.020979338</td><td>0.349754</td><td>0.434093</td><td>11865</td><td>1.0005217</td></tr>
<tr><td>7</td><td>S</td><td>1</td><td>0.63392232</td><td>0.6335725</td><td>0.029970502</td><td>0.573286</td><td>0.692213</td><td>22299</td><td>1.0003485</td></tr>
<tr><td>7</td><td>S</td><td>0</td><td>0.73233841</td><td>0.731793</td><td>0.014705269</td><td>0.703862</td><td>0.761504</td><td>40402</td><td>1.0000324</td></tr>
<tr><td>7</td><td>r</td><td>2</td><td>0.03033354</td><td>0.0303258</td><td>0.001806955</td><td>0.0267714</td><td>0.0340371</td><td>16816</td><td>1.0000747</td></tr>
<tr><td>7</td><td>r</td><td>1</td><td>0.04072984</td><td>0.0405291</td><td>0.004423129</td><td>0.0323523</td><td>0.0495407</td><td>24659</td><td>1.0001479</td></tr>
<tr><td>7</td><td>r</td><td>0</td><td>0.04822396</td><td>0.04800005</td><td>0.003411029</td><td>0.0418203</td><td>0.0551263</td><td>32574</td><td>1.0000925</td></tr>
<tr><td>8</td><td>S</td><td>2</td><td>0.39874486</td><td>0.3979</td><td>0.021071848</td><td>0.357277</td><td>0.44141</td><td>11115</td><td>1.0005182</td></tr>
<tr><td>8</td><td>S</td><td>1</td><td>0.65320405</td><td>0.6519455</td><td>0.030492956</td><td>0.593065</td><td>0.712376</td><td>19153</td><td>1.0003813</td></tr>
<tr><td>8</td><td>S</td><td>0</td><td>0.74151657</td><td>0.7407675</td><td>0.01589492</td><td>0.710841</td><td>0.772556</td><td>34186</td><td>1.0000337</td></tr>
<tr><td>8</td><td>r</td><td>2</td><td>0.02992005</td><td>0.0299576</td><td>0.00181893</td><td>0.0261842</td><td>0.033486</td><td>17705</td><td>1.0000968</td></tr>
<tr><td>8</td><td>r</td><td>1</td><td>0.04381966</td><td>0.04354045</td><td>0.004824345</td><td>0.0345815</td><td>0.0533255</td><td>23266</td><td>1.0002089</td></tr>
<tr><td>8</td><td>r</td><td>0</td><td>0.04619329</td><td>0.0460033</td><td>0.003271724</td><td>0.0400579</td><td>0.0528369</td><td>35424</td><td>1.0002091</td></tr>
<tr><td>9</td><td>S</td><td>2</td><td>0.39704562</td><td>0.3967755</td><td>0.020759787</td><td>0.355819</td><td>0.438718</td><td>11507</td><td>1.0004526</td></tr>
<tr><td>9</td><td>S</td><td>1</td><td>0.65450967</td><td>0.65346</td><td>0.029766449</td><td>0.596927</td><td>0.714032</td><td>18121</td><td>1.0002508</td></tr>
<tr><td>9</td><td>S</td><td>0</td><td>0.73292295</td><td>0.732307</td><td>0.014835285</td><td>0.704592</td><td>0.762375</td><td>38897</td><td>1.000102</td></tr>
<tr><td>9</td><td>r</td><td>2</td><td>0.02987766</td><td>0.0298808</td><td>0.001786338</td><td>0.0263627</td><td>0.033488</td><td>16953</td><td>1.0001617</td></tr>
<tr><td>9</td><td>r</td><td>1</td><td>0.05012642</td><td>0.0496006</td><td>0.005602082</td><td>0.039872</td><td>0.0613448</td><td>20417</td><td>1.0002076</td></tr>
<tr><td>9</td><td>r</td><td>0</td><td>0.04736969</td><td>0.0470909</td><td>0.00331456</td><td>0.0411836</td><td>0.053982</td><td>38218</td><td>1.0002425</td></tr>
<tr><td>10</td><td>S</td><td>2</td><td>0.39612097</td><td>0.396069</td><td>0.021191242</td><td>0.353474</td><td>0.438272</td><td>10984</td><td>1.0006151</td></tr>
<tr><td>10</td><td>S</td><td>1</td><td>0.66138247</td><td>0.6600305</td><td>0.030573637</td><td>0.603372</td><td>0.723023</td><td>16051</td><td>1.0000948</td></tr>
<tr><td>10</td><td>S</td><td>0</td><td>0.73457739</td><td>0.7337155</td><td>0.015717024</td><td>0.704985</td><td>0.766017</td><td>35033</td><td>1.0000707</td></tr>
<tr><td>10</td><td>r</td><td>2</td><td>0.02960736</td><td>0.02965095</td><td>0.001832297</td><td>0.0258006</td><td>0.0331344</td><td>16443</td><td>1.0001184</td></tr>
<tr><td>10</td><td>r</td><td>1</td><td>0.05545436</td><td>0.0547027</td><td>0.006817888</td><td>0.0430158</td><td>0.0689374</td><td>21183</td><td>1.0000625</td></tr>
<tr><td>10</td><td>r</td><td>0</td><td>0.0439056</td><td>0.04367665</td><td>0.002939139</td><td>0.0384369</td><td>0.0497926</td><td>42502</td><td>1.00024</td></tr>
<tr><td>11</td><td>S</td><td>2</td><td>0.39338615</td><td>0.3941885</td><td>0.022170422</td><td>0.3475</td><td>0.43679</td><td>11137</td><td>1.0006685</td></tr>
<tr><td>11</td><td>S</td><td>1</td><td>0.656912</td><td>0.6561695</td><td>0.031815904</td><td>0.596094</td><td>0.720881</td><td>16947</td><td>1.0002267</td></tr>
<tr><td>11</td><td>S</td><td>0</td><td>0.729328</td><td>0.728311</td><td>0.015903493</td><td>0.700324</td><td>0.761942</td><td>38489</td><td>0.9999803</td></tr>
<tr><td>11</td><td>r</td><td>2</td><td>0.02930209</td><td>0.02939715</td><td>0.001865798</td><td>0.0254331</td><td>0.0329187</td><td>14508</td><td>1.0001286</td></tr>
<tr><td>11</td><td>r</td><td>1</td><td>0.05374666</td><td>0.053038</td><td>0.006532963</td><td>0.0420272</td><td>0.0669492</td><td>17576</td><td>1.0000799</td></tr>
<tr><td>11</td><td>r</td><td>0</td><td>0.04084776</td><td>0.04067595</td><td>0.002657617</td><td>0.0357194</td><td>0.0461638</td><td>49426</td><td>1.0001557</td></tr>
<tr><td>12</td><td>S</td><td>2</td><td>0.38883649</td><td>0.391089</td><td>0.024074558</td><td>0.337882</td><td>0.433822</td><td>11051</td><td>1.0004463</td></tr>
<tr><td>12</td><td>S</td><td>1</td><td>0.66755155</td><td>0.6663505</td><td>0.032511997</td><td>0.606103</td><td>0.733357</td><td>14006</td><td>1.0002491</td></tr>
<tr><td>12</td><td>S</td><td>0</td><td>0.70798932</td><td>0.7079485</td><td>0.014783786</td><td>0.678356</td><td>0.737217</td><td>55222</td><td>0.9999959</td></tr>
<tr><td>12</td><td>r</td><td>2</td><td>0.02952811</td><td>0.0295437</td><td>0.001830899</td><td>0.0258296</td><td>0.0331434</td><td>15208</td><td>1.0000496</td></tr>
<tr><td>12</td><td>r</td><td>1</td><td>0.04943251</td><td>0.0488764</td><td>0.005889102</td><td>0.0382758</td><td>0.0610996</td><td>16264</td><td>1.0001538</td></tr>
<tr><td>12</td><td>r</td><td>0</td><td>0.03652721</td><td>0.03651575</td><td>0.002312926</td><td>0.031989</td><td>0.0410969</td><td>46676</td><td>1.0001095</td></tr>
<tr><td>13</td><td>S</td><td>2</td><td>0.39251688</td><td>0.39366</td><td>0.02260798</td><td>0.346557</td><td>0.436583</td><td>10674</td><td>1.0005214</td></tr>
<tr><td>13</td><td>S</td><td>1</td><td>0.67534459</td><td>0.67436</td><td>0.033345727</td><td>0.611026</td><td>0.740948</td><td>12408</td><td>1.0004021</td></tr>
<tr><td>13</td><td>S</td><td>0</td><td>0.68251465</td><td>0.6837935</td><td>0.017493897</td><td>0.64731</td><td>0.715032</td><td>40709</td><td>1.0000752</td></tr>
<tr><td>13</td><td>r</td><td>2</td><td>0.02961249</td><td>0.0295707</td><td>0.001849958</td><td>0.0259916</td><td>0.0333444</td><td>15153</td><td>1.0000452</td></tr>
<tr><td>13</td><td>r</td><td>1</td><td>0.04682449</td><td>0.04632355</td><td>0.005719077</td><td>0.0364081</td><td>0.0584248</td><td>14535</td><td>1.0002178</td></tr>
<tr><td>13</td><td>r</td><td>0</td><td>0.0341242</td><td>0.03412615</td><td>0.002306109</td><td>0.0293557</td><td>0.0384883</td><td>36350</td><td>1.0000394</td></tr>
<tr><td>14</td><td>S</td><td>2</td><td>0.39365646</td><td>0.3947365</td><td>0.023118566</td><td>0.345968</td><td>0.438907</td><td>10918</td><td>1.0004119</td></tr>
<tr><td>14</td><td>S</td><td>1</td><td>0.67201347</td><td>0.671151</td><td>0.03342434</td><td>0.607979</td><td>0.738266</td><td>11984</td><td>1.0004081</td></tr>
<tr><td>14</td><td>S</td><td>0</td><td>0.67593193</td><td>0.677124</td><td>0.017947717</td><td>0.640022</td><td>0.709513</td><td>37103</td><td>0.9999815</td></tr>
<tr><td>14</td><td>r</td><td>2</td><td>0.02940496</td><td>0.0293942</td><td>0.001822185</td><td>0.0257602</td><td>0.0329698</td><td>13778</td><td>1.0000813</td></tr>
<tr><td>14</td><td>r</td><td>1</td><td>0.04616664</td><td>0.04565695</td><td>0.00567249</td><td>0.0355083</td><td>0.0573115</td><td>13576</td><td>1.0001015</td></tr>
<tr><td>14</td><td>r</td><td>0</td><td>0.03126325</td><td>0.03129835</td><td>0.002167546</td><td>0.026906</td><td>0.0354075</td><td>33245</td><td>0.9999964</td></tr>
<tr><td>15</td><td>S</td><td>2</td><td>0.40217177</td><td>0.401206</td><td>0.023210076</td><td>0.357257</td><td>0.449697</td><td>9737</td><td>1.0002238</td></tr>
<tr><td>15</td><td>S</td><td>1</td><td>0.67144005</td><td>0.6707645</td><td>0.034502537</td><td>0.606679</td><td>0.740657</td><td>11716</td><td>1.0003366</td></tr>
<tr><td>15</td><td>S</td><td>0</td><td>0.67237339</td><td>0.6735915</td><td>0.01866596</td><td>0.63427</td><td>0.706767</td><td>37130</td><td>1.0000007</td></tr>
<tr><td>15</td><td>r</td><td>2</td><td>0.02898629</td><td>0.029017</td><td>0.001854252</td><td>0.0252948</td><td>0.0326018</td><td>12617</td><td>1.0002151</td></tr>
<tr><td>15</td><td>r</td><td>1</td><td>0.04603943</td><td>0.04542425</td><td>0.005906808</td><td>0.0354922</td><td>0.0581557</td><td>12927</td><td>1.0002092</td></tr>
<tr><td>15</td><td>r</td><td>0</td><td>0.03012251</td><td>0.0301302</td><td>0.002054781</td><td>0.0259781</td><td>0.0340424</td><td>36041</td><td>1.0000674</td></tr>
<tr><td>16</td><td>S</td><td>2</td><td>0.40471256</td><td>0.403233</td><td>0.024341494</td><td>0.357605</td><td>0.454579</td><td>8858</td><td>1.0001313</td></tr>
<tr><td>16</td><td>S</td><td>1</td><td>0.67882805</td><td>0.677979</td><td>0.036955927</td><td>0.609794</td><td>0.753535</td><td>10714</td><td>1.0005639</td></tr>
<tr><td>16</td><td>S</td><td>0</td><td>0.67695308</td><td>0.677527</td><td>0.01776686</td><td>0.642027</td><td>0.711571</td><td>42104</td><td>1.0000401</td></tr>
<tr><td>16</td><td>r</td><td>2</td><td>0.02865083</td><td>0.0287054</td><td>0.001915689</td><td>0.0248722</td><td>0.032394</td><td>11456</td><td>1.0002635</td></tr>
<tr><td>16</td><td>r</td><td>1</td><td>0.04283826</td><td>0.04223145</td><td>0.00586314</td><td>0.0319486</td><td>0.054381</td><td>11667</td><td>1.0003824</td></tr>
<tr><td>16</td><td>r</td><td>0</td><td>0.03053685</td><td>0.0304874</td><td>0.002012607</td><td>0.0266341</td><td>0.0345248</td><td>39577</td><td>1.0000237</td></tr>
<tr><td>17</td><td>S</td><td>2</td><td>0.40899132</td><td>0.406662</td><td>0.026347797</td><td>0.360254</td><td>0.464254</td><td>8469</td><td>1.0001152</td></tr>
<tr><td>17</td><td>S</td><td>1</td><td>0.68958486</td><td>0.688466</td><td>0.040881149</td><td>0.612309</td><td>0.770138</td><td>10125</td><td>1.0007053</td></tr>
<tr><td>17</td><td>S</td><td>0</td><td>0.68427325</td><td>0.684477</td><td>0.018891614</td><td>0.647325</td><td>0.721681</td><td>40704</td><td>1.0001205</td></tr>
<tr><td>17</td><td>r</td><td>2</td><td>0.02850114</td><td>0.02854915</td><td>0.001999802</td><td>0.0245983</td><td>0.0324138</td><td>11185</td><td>1.000224</td></tr>
<tr><td>17</td><td>r</td><td>1</td><td>0.03912155</td><td>0.0384691</td><td>0.006000874</td><td>0.0283236</td><td>0.0511685</td><td>11290</td><td>1.0006489</td></tr>
<tr><td>17</td><td>r</td><td>0</td><td>0.03122504</td><td>0.0310815</td><td>0.002216067</td><td>0.0270188</td><td>0.0356561</td><td>34584</td><td>1.0002149</td></tr>
<tr><td>18</td><td>S</td><td>2</td><td>0.41279886</td><td>0.409346</td><td>0.02879521</td><td>0.359902</td><td>0.472773</td><td>8073</td><td>1.0001421</td></tr>
<tr><td>18</td><td>S</td><td>1</td><td>0.69106253</td><td>0.69015</td><td>0.043519567</td><td>0.610512</td><td>0.778436</td><td>10153</td><td>1.0007292</td></tr>
<tr><td>18</td><td>S</td><td>0</td><td>0.68844005</td><td>0.6884165</td><td>0.022636328</td><td>0.644645</td><td>0.734478</td><td>35121</td><td>1.0001275</td></tr>
<tr><td>18</td><td>r</td><td>2</td><td>0.02842064</td><td>0.0284655</td><td>0.002074276</td><td>0.0243475</td><td>0.0323895</td><td>11160</td><td>1.0002673</td></tr>
<tr><td>18</td><td>r</td><td>1</td><td>0.03922429</td><td>0.0384712</td><td>0.006382935</td><td>0.0279443</td><td>0.0521398</td><td>10627</td><td>1.0007579</td></tr>
<tr><td>18</td><td>r</td><td>0</td><td>0.03099367</td><td>0.0307985</td><td>0.002420974</td><td>0.0262671</td><td>0.035697</td><td>32023</td><td>1.0001478</td></tr>
<tr><td>19</td><td>S</td><td>2</td><td>0.41797347</td><td>0.412787</td><td>0.032931518</td><td>0.359831</td><td>0.48691</td><td>8219</td><td>1.0001959</td></tr>
<tr><td>19</td><td>S</td><td>1</td><td>0.69548581</td><td>0.694373</td><td>0.046403023</td><td>0.60811</td><td>0.787225</td><td>10316</td><td>1.0005753</td></tr>
<tr><td>19</td><td>S</td><td>0</td><td>0.69498736</td><td>0.694472</td><td>0.028982882</td><td>0.638653</td><td>0.754275</td><td>30613</td><td>1.000036</td></tr>
<tr><td>19</td><td>r</td><td>2</td><td>0.02827305</td><td>0.0283371</td><td>0.002221669</td><td>0.0238392</td><td>0.0324905</td><td>11511</td><td>1.0001899</td></tr>
<tr><td>19</td><td>r</td><td>1</td><td>0.03975637</td><td>0.0388706</td><td>0.006882775</td><td>0.0277338</td><td>0.0536675</td><td>11151</td><td>1.0007562</td></tr>
<tr><td>19</td><td>r</td><td>0</td><td>0.02910771</td><td>0.02893705</td><td>0.002584023</td><td>0.0243042</td><td>0.0344336</td><td>30856</td><td>1.0000823</td></tr>
<tr><td>20</td><td>S</td><td>2</td><td>0.41626253</td><td>0.4114665</td><td>0.033658356</td><td>0.35576</td><td>0.487597</td><td>9133</td><td>1.0001021</td></tr>
<tr><td>20</td><td>S</td><td>1</td><td>0.68222125</td><td>0.681819</td><td>0.048973917</td><td>0.5893</td><td>0.78065</td><td>12785</td><td>1.0004403</td></tr>
<tr><td>20</td><td>S</td><td>0</td><td>0.69149582</td><td>0.69189</td><td>0.035496763</td><td>0.620264</td><td>0.762961</td><td>32896</td><td>1.0000275</td></tr>
<tr><td>20</td><td>r</td><td>2</td><td>0.02854897</td><td>0.02858105</td><td>0.002335328</td><td>0.0237071</td><td>0.0328704</td><td>14118</td><td>1.0002007</td></tr>
<tr><td>20</td><td>r</td><td>1</td><td>0.04396876</td><td>0.04285475</td><td>0.007863741</td><td>0.0305334</td><td>0.0598542</td><td>12697</td><td>1.00055</td></tr>
<tr><td>20</td><td>r</td><td>0</td><td>0.029754</td><td>0.02951105</td><td>0.002931291</td><td>0.0241819</td><td>0.0355893</td><td>32325</td><td>1.0001482</td></tr>
</table>
</details>

<details>
<summary>M<sub>sep</sub> (RW2) excl. tau</summary>
<table style="margin: 0 auto; border-collapse: collapse; width: auto;">
<tr><th>Time</th><th>Param</th><th>Age</th><th>Mean</th><th>Median</th><th>StdErr</th><th>Lower</th><th>Upper</th><th>ESS</th><th>$\hat{r}$</th></tr>
<tr><td>1</td><td>S</td><td>2</td><td>0.42200642</td><td>0.4200005</td><td>0.036575861</td><td>0.354557</td><td>0.498336</td><td>3062</td><td>1.000964</td></tr>
<tr><td>1</td><td>S</td><td>1</td><td>0.52410656</td><td>0.523434</td><td>0.038464469</td><td>0.449474</td><td>0.600182</td><td>3249</td><td>1.000209</td></tr>
<tr><td>1</td><td>S</td><td>0</td><td>0.69613047</td><td>0.698982</td><td>0.032967723</td><td>0.6289</td><td>0.756666</td><td>4673</td><td>1.002789</td></tr>
<tr><td>1</td><td>r</td><td>2</td><td>0.03629138</td><td>0.0358285</td><td>0.003827181</td><td>0.0295936</td><td>0.0442323</td><td>2689</td><td>1.002017</td></tr>
<tr><td>1</td><td>r</td><td>1</td><td>0.05239396</td><td>0.0518936</td><td>0.006146494</td><td>0.0408625</td><td>0.064627</td><td>4876</td><td>1.00019</td></tr>
<tr><td>1</td><td>r</td><td>0</td><td>0.06204744</td><td>0.06156115</td><td>0.007389722</td><td>0.0477156</td><td>0.0769618</td><td>5237</td><td>1.001007</td></tr>
<tr><td>2</td><td>S</td><td>2</td><td>0.41813198</td><td>0.4170105</td><td>0.028869423</td><td>0.362319</td><td>0.475017</td><td>2717</td><td>1.000988</td></tr>
<tr><td>2</td><td>S</td><td>1</td><td>0.53890925</td><td>0.5384085</td><td>0.030711423</td><td>0.480392</td><td>0.600603</td><td>2412</td><td>1.000398</td></tr>
<tr><td>2</td><td>S</td><td>0</td><td>0.70774814</td><td>0.708611</td><td>0.021547734</td><td>0.665329</td><td>0.74941</td><td>3692</td><td>1.00326</td></tr>
<tr><td>2</td><td>r</td><td>2</td><td>0.03484472</td><td>0.03465085</td><td>0.002736767</td><td>0.0297282</td><td>0.0403658</td><td>2229</td><td>1.001894</td></tr>
<tr><td>2</td><td>r</td><td>1</td><td>0.04548316</td><td>0.0452045</td><td>0.004225882</td><td>0.0376317</td><td>0.0540499</td><td>3415</td><td>1.000127</td></tr>
<tr><td>2</td><td>r</td><td>0</td><td>0.05817782</td><td>0.05811505</td><td>0.004833604</td><td>0.0486142</td><td>0.0677685</td><td>2760</td><td>1.002245</td></tr>
<tr><td>3</td><td>S</td><td>2</td><td>0.41122926</td><td>0.410563</td><td>0.024223761</td><td>0.363246</td><td>0.457913</td><td>2548</td><td>1.000847</td></tr>
<tr><td>3</td><td>S</td><td>1</td><td>0.55148038</td><td>0.5518305</td><td>0.028687945</td><td>0.493637</td><td>0.606969</td><td>2217</td><td>1.000895</td></tr>
<tr><td>3</td><td>S</td><td>0</td><td>0.71649909</td><td>0.716748</td><td>0.016012311</td><td>0.684561</td><td>0.747803</td><td>4052</td><td>1.002766</td></tr>
<tr><td>3</td><td>r</td><td>2</td><td>0.03357597</td><td>0.03349335</td><td>0.002167175</td><td>0.0295055</td><td>0.0379747</td><td>2103</td><td>1.001395</td></tr>
<tr><td>3</td><td>r</td><td>1</td><td>0.04036736</td><td>0.0402106</td><td>0.003705511</td><td>0.0332409</td><td>0.04775</td><td>3211</td><td>1.000491</td></tr>
<tr><td>3</td><td>r</td><td>0</td><td>0.05509819</td><td>0.0552078</td><td>0.003872798</td><td>0.0472507</td><td>0.0625283</td><td>1871</td><td>1.002959</td></tr>
<tr><td>4</td><td>S</td><td>2</td><td>0.4038022</td><td>0.403553</td><td>0.021871259</td><td>0.362277</td><td>0.448372</td><td>2561</td><td>1.000581</td></tr>
<tr><td>4</td><td>S</td><td>1</td><td>0.56652492</td><td>0.5675745</td><td>0.029119114</td><td>0.506851</td><td>0.622494</td><td>2109</td><td>1.00146</td></tr>
<tr><td>4</td><td>S</td><td>0</td><td>0.71734364</td><td>0.7183595</td><td>0.014429044</td><td>0.688481</td><td>0.744977</td><td>2694</td><td>1.003703</td></tr>
<tr><td>4</td><td>r</td><td>2</td><td>0.03263654</td><td>0.0325844</td><td>0.001899886</td><td>0.0288728</td><td>0.0364125</td><td>2026</td><td>1.001091</td></tr>
<tr><td>4</td><td>r</td><td>1</td><td>0.03793278</td><td>0.0377984</td><td>0.003519336</td><td>0.0310733</td><td>0.0448908</td><td>3059</td><td>1.000727</td></tr>
<tr><td>4</td><td>r</td><td>0</td><td>0.05331295</td><td>0.05340225</td><td>0.003089058</td><td>0.047064</td><td>0.0592803</td><td>2135</td><td>1.001821</td></tr>
<tr><td>5</td><td>S</td><td>2</td><td>0.39771548</td><td>0.3979565</td><td>0.020964725</td><td>0.355894</td><td>0.438506</td><td>2500</td><td>1.000485</td></tr>
<tr><td>5</td><td>S</td><td>1</td><td>0.58687254</td><td>0.58777</td><td>0.028241121</td><td>0.53008</td><td>0.642751</td><td>2112</td><td>1.001983</td></tr>
<tr><td>5</td><td>S</td><td>0</td><td>0.72043232</td><td>0.721774</td><td>0.01352639</td><td>0.692843</td><td>0.74569</td><td>2933</td><td>1.002449</td></tr>
<tr><td>5</td><td>r</td><td>2</td><td>0.03198222</td><td>0.0319251</td><td>0.001772849</td><td>0.0284901</td><td>0.035486</td><td>1939</td><td>1.001103</td></tr>
<tr><td>5</td><td>r</td><td>1</td><td>0.03746449</td><td>0.03733385</td><td>0.003496571</td><td>0.0307094</td><td>0.0443916</td><td>2998</td><td>1.001136</td></tr>
<tr><td>5</td><td>r</td><td>0</td><td>0.05167246</td><td>0.05171335</td><td>0.002666204</td><td>0.0463988</td><td>0.0570377</td><td>2839</td><td>1.001179</td></tr>
<tr><td>6</td><td>S</td><td>2</td><td>0.39424477</td><td>0.394655</td><td>0.020703471</td><td>0.353873</td><td>0.435308</td><td>2491</td><td>1.000802</td></tr>
<tr><td>6</td><td>S</td><td>1</td><td>0.6103281</td><td>0.610286</td><td>0.027229333</td><td>0.555694</td><td>0.66424</td><td>2058</td><td>1.002092</td></tr>
<tr><td>6</td><td>S</td><td>0</td><td>0.72489238</td><td>0.7258475</td><td>0.012023555</td><td>0.700891</td><td>0.748457</td><td>4972</td><td>1.001395</td></tr>
<tr><td>6</td><td>r</td><td>2</td><td>0.03118662</td><td>0.03115225</td><td>0.001705644</td><td>0.0277749</td><td>0.0345195</td><td>1907</td><td>1.000891</td></tr>
<tr><td>6</td><td>r</td><td>1</td><td>0.0386185</td><td>0.0384201</td><td>0.003701301</td><td>0.0314517</td><td>0.0459018</td><td>3016</td><td>1.001334</td></tr>
<tr><td>6</td><td>r</td><td>0</td><td>0.05051072</td><td>0.050331</td><td>0.002506865</td><td>0.0457974</td><td>0.0556495</td><td>2790</td><td>1.002029</td></tr>
<tr><td>7</td><td>S</td><td>2</td><td>0.3920145</td><td>0.3922615</td><td>0.020961913</td><td>0.350275</td><td>0.433009</td><td>2533</td><td>1.001301</td></tr>
<tr><td>7</td><td>S</td><td>1</td><td>0.6338783</td><td>0.6322905</td><td>0.028061925</td><td>0.581787</td><td>0.691155</td><td>1974</td><td>1.001531</td></tr>
<tr><td>7</td><td>S</td><td>0</td><td>0.7337967</td><td>0.733146</td><td>0.011201717</td><td>0.71197</td><td>0.756256</td><td>4259</td><td>1.001602</td></tr>
<tr><td>7</td><td>r</td><td>2</td><td>0.0304218</td><td>0.0304421</td><td>0.001708898</td><td>0.0270069</td><td>0.0338155</td><td>1828</td><td>1.00123</td></tr>
<tr><td>7</td><td>r</td><td>1</td><td>0.04084799</td><td>0.04054815</td><td>0.004202742</td><td>0.0327677</td><td>0.0491697</td><td>2944</td><td>1.001084</td></tr>
<tr><td>7</td><td>r</td><td>0</td><td>0.0487566</td><td>0.048441</td><td>0.002557503</td><td>0.0442485</td><td>0.0540852</td><td>2213</td><td>1.004434</td></tr>
<tr><td>8</td><td>S</td><td>2</td><td>0.3910713</td><td>0.390985</td><td>0.021463353</td><td>0.348576</td><td>0.433383</td><td>2426</td><td>1.001426</td></tr>
<tr><td>8</td><td>S</td><td>1</td><td>0.65419329</td><td>0.651681</td><td>0.02982787</td><td>0.598291</td><td>0.713932</td><td>1778</td><td>1.000758</td></tr>
<tr><td>8</td><td>S</td><td>0</td><td>0.73845976</td><td>0.736838</td><td>0.013789374</td><td>0.713112</td><td>0.766178</td><td>2017</td><td>1.003745</td></tr>
<tr><td>8</td><td>r</td><td>2</td><td>0.02980811</td><td>0.02986255</td><td>0.001728333</td><td>0.0262562</td><td>0.0331528</td><td>1700</td><td>1.00168</td></tr>
<tr><td>8</td><td>r</td><td>1</td><td>0.04550966</td><td>0.04501345</td><td>0.004962539</td><td>0.0365525</td><td>0.0557591</td><td>2583</td><td>1.000759</td></tr>
<tr><td>8</td><td>r</td><td>0</td><td>0.04699981</td><td>0.0465453</td><td>0.002679298</td><td>0.0426102</td><td>0.0526993</td><td>1781</td><td>1.005816</td></tr>
<tr><td>9</td><td>S</td><td>2</td><td>0.38814516</td><td>0.388222</td><td>0.021724819</td><td>0.343184</td><td>0.429412</td><td>2425</td><td>1.001347</td></tr>
<tr><td>9</td><td>S</td><td>1</td><td>0.66816157</td><td>0.6657485</td><td>0.030221883</td><td>0.612874</td><td>0.730603</td><td>1673</td><td>1.000361</td></tr>
<tr><td>9</td><td>S</td><td>0</td><td>0.73511204</td><td>0.7335495</td><td>0.013649435</td><td>0.710714</td><td>0.763416</td><td>1880</td><td>1.004464</td></tr>
<tr><td>9</td><td>r</td><td>2</td><td>0.02936511</td><td>0.02943865</td><td>0.001715554</td><td>0.0258344</td><td>0.0326474</td><td>1671</td><td>1.001642</td></tr>
<tr><td>9</td><td>r</td><td>1</td><td>0.05263268</td><td>0.05182635</td><td>0.006242269</td><td>0.0415027</td><td>0.0651314</td><td>2284</td><td>1.000267</td></tr>
<tr><td>9</td><td>r</td><td>0</td><td>0.04536231</td><td>0.04471945</td><td>0.002971258</td><td>0.0406357</td><td>0.0515575</td><td>1729</td><td>1.006305</td></tr>
<tr><td>10</td><td>S</td><td>2</td><td>0.38424425</td><td>0.384672</td><td>0.022183678</td><td>0.338467</td><td>0.426524</td><td>2094</td><td>1.001739</td></tr>
<tr><td>10</td><td>S</td><td>1</td><td>0.67895113</td><td>0.676642</td><td>0.030548327</td><td>0.61906</td><td>0.738801</td><td>1484</td><td>1.000263</td></tr>
<tr><td>10</td><td>S</td><td>0</td><td>0.73098684</td><td>0.7290375</td><td>0.01424259</td><td>0.706519</td><td>0.760998</td><td>1958</td><td>1.005083</td></tr>
<tr><td>10</td><td>r</td><td>2</td><td>0.02901636</td><td>0.0290929</td><td>0.001709631</td><td>0.0255111</td><td>0.0323319</td><td>1699</td><td>1.001391</td></tr>
<tr><td>10</td><td>r</td><td>1</td><td>0.05856806</td><td>0.0575555</td><td>0.007648168</td><td>0.0451443</td><td>0.0738226</td><td>2089</td><td>1.000152</td></tr>
<tr><td>10</td><td>r</td><td>0</td><td>0.04274779</td><td>0.04228045</td><td>0.002441243</td><td>0.0388015</td><td>0.0479689</td><td>2224</td><td>1.005586</td></tr>
<tr><td>11</td><td>S</td><td>2</td><td>0.37992292</td><td>0.3808865</td><td>0.023318213</td><td>0.333301</td><td>0.425204</td><td>1897</td><td>1.001994</td></tr>
<tr><td>11</td><td>S</td><td>1</td><td>0.68737061</td><td>0.686006</td><td>0.031168609</td><td>0.6276</td><td>0.749863</td><td>1391</td><td>1.000314</td></tr>
<tr><td>11</td><td>S</td><td>0</td><td>0.72272814</td><td>0.721103</td><td>0.012965019</td><td>0.699251</td><td>0.749828</td><td>2856</td><td>1.004256</td></tr>
<tr><td>11</td><td>r</td><td>2</td><td>0.02877082</td><td>0.028822</td><td>0.001693404</td><td>0.0253769</td><td>0.0321175</td><td>1816</td><td>1.001457</td></tr>
<tr><td>11</td><td>r</td><td>1</td><td>0.05924333</td><td>0.05828245</td><td>0.007782099</td><td>0.0455085</td><td>0.0748743</td><td>1951</td><td>1.000304</td></tr>
<tr><td>11</td><td>r</td><td>0</td><td>0.03977181</td><td>0.03959005</td><td>0.00179504</td><td>0.0364414</td><td>0.0435741</td><td>3629</td><td>1.002593</td></tr>
<tr><td>12</td><td>S</td><td>2</td><td>0.37697279</td><td>0.378578</td><td>0.02478313</td><td>0.326542</td><td>0.424173</td><td>1787</td><td>1.002153</td></tr>
<tr><td>12</td><td>S</td><td>1</td><td>0.6960911</td><td>0.6951275</td><td>0.031912802</td><td>0.637075</td><td>0.762527</td><td>1266</td><td>1.000579</td></tr>
<tr><td>12</td><td>S</td><td>0</td><td>0.70886201</td><td>0.708841</td><td>0.010967658</td><td>0.686571</td><td>0.730723</td><td>9020</td><td>1.000828</td></tr>
<tr><td>12</td><td>r</td><td>2</td><td>0.02866468</td><td>0.02866385</td><td>0.001663722</td><td>0.0252694</td><td>0.0318953</td><td>1897</td><td>1.00189</td></tr>
<tr><td>12</td><td>r</td><td>1</td><td>0.05620282</td><td>0.05536215</td><td>0.007207677</td><td>0.0431492</td><td>0.0705012</td><td>1728</td><td>1.00052</td></tr>
<tr><td>12</td><td>r</td><td>0</td><td>0.03680248</td><td>0.036899</td><td>0.001656526</td><td>0.0333011</td><td>0.0399926</td><td>3281</td><td>1.000919</td></tr>
<tr><td>13</td><td>S</td><td>2</td><td>0.37828087</td><td>0.379593</td><td>0.02468512</td><td>0.32895</td><td>0.426234</td><td>1834</td><td>1.002153</td></tr>
<tr><td>13</td><td>S</td><td>1</td><td>0.70273291</td><td>0.702054</td><td>0.032664334</td><td>0.638475</td><td>0.767213</td><td>1147</td><td>1.000942</td></tr>
<tr><td>13</td><td>S</td><td>0</td><td>0.69357642</td><td>0.6956095</td><td>0.014331221</td><td>0.663984</td><td>0.719652</td><td>2890</td><td>1.001328</td></tr>
<tr><td>13</td><td>r</td><td>2</td><td>0.02857048</td><td>0.02852975</td><td>0.001665999</td><td>0.0253332</td><td>0.0318944</td><td>2019</td><td>1.002534</td></tr>
<tr><td>13</td><td>r</td><td>1</td><td>0.05354365</td><td>0.0527927</td><td>0.007084263</td><td>0.0404779</td><td>0.0673125</td><td>1375</td><td>1.000822</td></tr>
<tr><td>13</td><td>r</td><td>0</td><td>0.03426857</td><td>0.03453905</td><td>0.001851378</td><td>0.0303116</td><td>0.0375219</td><td>1950</td><td>1.003396</td></tr>
<tr><td>14</td><td>S</td><td>2</td><td>0.38255157</td><td>0.38327</td><td>0.024169376</td><td>0.334228</td><td>0.429864</td><td>1811</td><td>1.002434</td></tr>
<tr><td>14</td><td>S</td><td>1</td><td>0.70650307</td><td>0.7060555</td><td>0.034516816</td><td>0.639691</td><td>0.774669</td><td>1014</td><td>1.001286</td></tr>
<tr><td>14</td><td>S</td><td>0</td><td>0.68462689</td><td>0.6872235</td><td>0.016643924</td><td>0.649986</td><td>0.713895</td><td>2102</td><td>1.002638</td></tr>
<tr><td>14</td><td>r</td><td>2</td><td>0.02841719</td><td>0.02836095</td><td>0.001672561</td><td>0.025173</td><td>0.0317231</td><td>1929</td><td>1.002903</td></tr>
<tr><td>14</td><td>r</td><td>1</td><td>0.05236358</td><td>0.0515197</td><td>0.007358212</td><td>0.0392609</td><td>0.0667599</td><td>1139</td><td>1.00146</td></tr>
<tr><td>14</td><td>r</td><td>0</td><td>0.0320861</td><td>0.0324257</td><td>0.001999724</td><td>0.0279381</td><td>0.0355285</td><td>1686</td><td>1.004126</td></tr>
<tr><td>15</td><td>S</td><td>2</td><td>0.3902389</td><td>0.389913</td><td>0.024133199</td><td>0.343504</td><td>0.437832</td><td>1660</td><td>1.003334</td></tr>
<tr><td>15</td><td>S</td><td>1</td><td>0.71050671</td><td>0.7103505</td><td>0.037877952</td><td>0.636756</td><td>0.784811</td><td>806</td><td>1.001999</td></tr>
<tr><td>15</td><td>S</td><td>0</td><td>0.67966835</td><td>0.681914</td><td>0.016770753</td><td>0.645369</td><td>0.710119</td><td>2186</td><td>1.002399</td></tr>
<tr><td>15</td><td>r</td><td>2</td><td>0.02821476</td><td>0.02815705</td><td>0.001703325</td><td>0.0248712</td><td>0.031517</td><td>1652</td><td>1.003125</td></tr>
<tr><td>15</td><td>r</td><td>1</td><td>0.05118932</td><td>0.05021325</td><td>0.00790148</td><td>0.0372745</td><td>0.0665981</td><td>860</td><td>1.002783</td></tr>
<tr><td>15</td><td>r</td><td>0</td><td>0.03060127</td><td>0.0307985</td><td>0.001793755</td><td>0.0268375</td><td>0.0337022</td><td>1771</td><td>1.003041</td></tr>
<tr><td>16</td><td>S</td><td>2</td><td>0.39803222</td><td>0.397109</td><td>0.026120921</td><td>0.347329</td><td>0.449266</td><td>1421</td><td>1.004112</td></tr>
<tr><td>16</td><td>S</td><td>1</td><td>0.71689726</td><td>0.7169495</td><td>0.041955924</td><td>0.634787</td><td>0.798763</td><td>737</td><td>1.002979</td></tr>
<tr><td>16</td><td>S</td><td>0</td><td>0.67766121</td><td>0.678656</td><td>0.015524134</td><td>0.645884</td><td>0.706648</td><td>3294</td><td>1.001007</td></tr>
<tr><td>16</td><td>r</td><td>2</td><td>0.02803914</td><td>0.02795625</td><td>0.001801002</td><td>0.0245241</td><td>0.0315359</td><td>1478</td><td>1.003222</td></tr>
<tr><td>16</td><td>r</td><td>1</td><td>0.04820726</td><td>0.04706655</td><td>0.008433028</td><td>0.0338618</td><td>0.0647509</td><td>711</td><td>1.0047</td></tr>
<tr><td>16</td><td>r</td><td>0</td><td>0.02977265</td><td>0.02981655</td><td>0.001480945</td><td>0.0268246</td><td>0.0326324</td><td>2676</td><td>1.001054</td></tr>
<tr><td>17</td><td>S</td><td>2</td><td>0.40637575</td><td>0.404815</td><td>0.029896357</td><td>0.346602</td><td>0.463488</td><td>1304</td><td>1.005008</td></tr>
<tr><td>17</td><td>S</td><td>1</td><td>0.72429348</td><td>0.7250205</td><td>0.046985426</td><td>0.632063</td><td>0.816001</td><td>730</td><td>1.003881</td></tr>
<tr><td>17</td><td>S</td><td>0</td><td>0.6773408</td><td>0.67731</td><td>0.017066977</td><td>0.643235</td><td>0.710464</td><td>3967</td><td>1.001987</td></tr>
<tr><td>17</td><td>r</td><td>2</td><td>0.02794072</td><td>0.027828</td><td>0.001988067</td><td>0.0241435</td><td>0.0318724</td><td>1432</td><td>1.003428</td></tr>
<tr><td>17</td><td>r</td><td>1</td><td>0.04527977</td><td>0.04399165</td><td>0.009292325</td><td>0.0295275</td><td>0.0631423</td><td>640</td><td>1.006513</td></tr>
<tr><td>17</td><td>r</td><td>0</td><td>0.02925955</td><td>0.0291148</td><td>0.001747642</td><td>0.0260808</td><td>0.0328954</td><td>2208</td><td>1.00265</td></tr>
<tr><td>18</td><td>S</td><td>2</td><td>0.4150349</td><td>0.4127455</td><td>0.035404816</td><td>0.347292</td><td>0.485892</td><td>1198</td><td>1.005135</td></tr>
<tr><td>18</td><td>S</td><td>1</td><td>0.72959987</td><td>0.7321555</td><td>0.054131477</td><td>0.623628</td><td>0.834712</td><td>773</td><td>1.006258</td></tr>
<tr><td>18</td><td>S</td><td>0</td><td>0.67754834</td><td>0.6759685</td><td>0.024839574</td><td>0.630372</td><td>0.729316</td><td>2365</td><td>1.005221</td></tr>
<tr><td>18</td><td>r</td><td>2</td><td>0.02793148</td><td>0.02775735</td><td>0.002284948</td><td>0.0235972</td><td>0.0324577</td><td>1359</td><td>1.00352</td></tr>
<tr><td>18</td><td>r</td><td>1</td><td>0.04527973</td><td>0.0437528</td><td>0.010912848</td><td>0.0270967</td><td>0.0658782</td><td>628</td><td>1.007486</td></tr>
<tr><td>18</td><td>r</td><td>0</td><td>0.02870868</td><td>0.0282817</td><td>0.002447551</td><td>0.0246089</td><td>0.033766</td><td>1635</td><td>1.005267</td></tr>
<tr><td>19</td><td>S</td><td>2</td><td>0.42366416</td><td>0.420551</td><td>0.042924159</td><td>0.341129</td><td>0.508219</td><td>1302</td><td>1.0053</td></tr>
<tr><td>19</td><td>S</td><td>1</td><td>0.73288709</td><td>0.738117</td><td>0.063750373</td><td>0.607691</td><td>0.854894</td><td>825</td><td>1.005836</td></tr>
<tr><td>19</td><td>S</td><td>0</td><td>0.67789381</td><td>0.6739805</td><td>0.037956979</td><td>0.605745</td><td>0.758597</td><td>1989</td><td>1.006525</td></tr>
<tr><td>19</td><td>r</td><td>2</td><td>0.02801258</td><td>0.0277319</td><td>0.002759203</td><td>0.0229782</td><td>0.0336246</td><td>1430</td><td>1.003561</td></tr>
<tr><td>19</td><td>r</td><td>1</td><td>0.04807509</td><td>0.04605255</td><td>0.013719141</td><td>0.0261093</td><td>0.0738797</td><td>707</td><td>1.007621</td></tr>
<tr><td>19</td><td>r</td><td>0</td><td>0.02804824</td><td>0.0273469</td><td>0.003291637</td><td>0.0230277</td><td>0.0348987</td><td>1664</td><td>1.006082</td></tr>
<tr><td>20</td><td>S</td><td>2</td><td>0.43069065</td><td>0.425858</td><td>0.052623031</td><td>0.332299</td><td>0.535493</td><td>1489</td><td>1.004955</td></tr>
<tr><td>20</td><td>S</td><td>1</td><td>0.73191354</td><td>0.7413755</td><td>0.077946761</td><td>0.567689</td><td>0.867121</td><td>894</td><td>1.004773</td></tr>
<tr><td>20</td><td>S</td><td>0</td><td>0.67406885</td><td>0.6688715</td><td>0.052977512</td><td>0.574488</td><td>0.790276</td><td>2180</td><td>1.006331</td></tr>
<tr><td>20</td><td>r</td><td>2</td><td>0.02822402</td><td>0.02776735</td><td>0.003508035</td><td>0.0221024</td><td>0.0354483</td><td>1653</td><td>1.003432</td></tr>
<tr><td>20</td><td>r</td><td>1</td><td>0.05476646</td><td>0.051651</td><td>0.018901037</td><td>0.0257746</td><td>0.0894911</td><td>744</td><td>1.007194</td></tr>
<tr><td>20</td><td>r</td><td>0</td><td>0.0277677</td><td>0.02659635</td><td>0.004721706</td><td>0.0211733</td><td>0.0372565</td><td>1780</td><td>1.006235</td></tr>
</table>
</details>

<details>
<summary>M<sub>sep</sub> (RW1) tau</summary>
<table style="margin: 0 auto; border-collapse: collapse; width: auto;">
<tr><th>Param</th><th>Age</th><th>Mean</th><th>Median</th><th>StdErr</th><th>Lower</th><th>Upper</th><th>ESS</th><th>$\hat{r}$</th></tr>
<tr><td>S</td><td>2</td><td>3.47E+08</td><td>471.966</td><td>2.78E+10</td><td>3.95262</td><td>48662.3</td><td>13305</td><td>1.001862</td></tr>
<tr><td>S</td><td>1</td><td>2.72E+03</td><td>89.29325</td><td>1.06E+05</td><td>6.11636</td><td>557.099</td><td>9138</td><td>1.004997</td></tr>
<tr><td>S</td><td>0</td><td>2.87E+02</td><td>106.468</td><td>4.60E+03</td><td>9.89622</td><td>543.693</td><td>16120</td><td>1.000286</td></tr>
<tr><td>r</td><td>2</td><td>4.54E+06</td><td>578.0755</td><td>6.01E+08</td><td>9.32838</td><td>22261.4</td><td>14058</td><td>1.004208</td></tr>
<tr><td>r</td><td>1</td><td>1.52E+02</td><td>62.6558</td><td>2.42E+03</td><td>9.73621</td><td>229.412</td><td>14181</td><td>1.000368</td></tr>
<tr><td>r</td><td>0</td><td>1.22E+02</td><td>102.48</td><td>7.79E+01</td><td>17.9461</td><td>273.591</td><td>45715</td><td>1.000027</td></tr>
</table>
</details>

<details>
<summary>M<sub>sep</sub> (RW2) tau</summary>
<table style="margin: 0 auto; border-collapse: collapse; width: auto;">
<tr><th>Param</th><th>Age</th><th>Mean</th><th>Median</th><th>StdErr</th><th>Lower</th><th>Upper</th><th>ESS</th><th>$\hat{r}$</th></tr>
<tr><td>S</td><td>2.00E+00</td><td>59985.8019</td><td>1.30E+03</td><td>1.05E+06</td><td>3.93412</td><td>68422.9</td><td>6502</td><td>1.002518</td></tr>
<tr><td>S</td><td>1.00E+00</td><td>3420181.383</td><td>7.11E+02</td><td>2.08E+08</td><td>4.37589</td><td>52432.2</td><td>4209</td><td>1.001278</td></tr>
<tr><td>S</td><td>0.00E+00</td><td>18216.6537</td><td>6.45E+02</td><td>8.55E+05</td><td>6.1875</td><td>12940.5</td><td>6332</td><td>1.00505</td></tr>
<tr><td>r</td><td>2.00E+00</td><td>2013187.254</td><td>4.26E+03</td><td>6.47E+07</td><td>11.9748</td><td>411014</td><td>4717</td><td>1.007814</td></tr>
<tr><td>r</td><td>1.00E+00</td><td>198.4221</td><td>1.11E+02</td><td>1.19E+03</td><td>9.48476</td><td>476.69</td><td>11362</td><td>1.000201</td></tr>
<tr><td>r</td><td>0</td><td>4665046.632</td><td>1685.485</td><td>4.44E+08</td><td>7.89426</td><td>325266</td><td>10034</td><td>1.00497</td></tr>
</table>
</details>

### R code

The R code to obtain the estiamtes from the GLMs and Bayesian models are below

<details>
<summary>GLM Brownie</summary>
<pre>
<code><![CDATA[
library(readxl)
library(dplyr)
library(tidyr)
library(ggplot2)

setwd(dirname(parent.frame(2)$ofile))

windowsFonts(Font = windowsFont("CMU Serif"))

#Read data - you may need to change these
adults    <- read_xlsx("../data/blackbirds/adults.xlsx")
juveniles <- read_xlsx("../data/blackbirds/juveniles.xlsx")
pulliSep  <- read_xlsx("../data/blackbirds/pulli.xlsx")
pulliComb <- read_xlsx("../data/blackbirds/pulliComb.xlsx")

#Select recovery counts
R     <- adults    %>% select(-c("Year of ringing", "Total ringed"))
Q     <- juveniles %>% select(-c("Year of ringing", "Total ringed"))
PSep  <- pulliSep  %>% select(-c("Year of ringing", "Total ringed"))
PComb <- pulliComb %>% select(-c("Year of ringing", "Total ringed"))

#Select totals ringed
N <- adults    %>% select("Total ringed")
M <- juveniles %>% select("Total ringed")
L <- pulliSep  %>% select("Total ringed")

#Convert recovery counts to matrix
R     <- as.matrix(R)
Q     <- as.matrix(Q)
PSep  <- as.matrix(PSep)
PComb <- as.matrix(PComb)

#Convert totals ringed to vectors
N <- as.vector(N)$`Total ringed`
M <- as.vector(M)$`Total ringed`
L <- as.vector(L)$`Total ringed`

#Inverse logit function
ilogit <- function(x){
  1/(1+exp(-x))
}

#Likelihood function for separate post-fledging recoveries
likelihood3A_PFsep <- function(params,
                               R, P, Q,
                               N, M, L){
  l <- dim(R)[1]
  
  #Parameters in logit space
  logitf0 <- params[1:l]
  logitf1 <- params[(l+1):(2*l)]
  logitf2 <- params[(2*l+1):(3*l)]
  logitS0 <- params[(3*l+1):(4*l-1)]
  logitS1 <- params[(4*l):(5*l-2)]
  logitS2 <- params[(5*l-1):(6*l-2)]
  
  #Parameters bounded between 0 and 1
  f0 <- ilogit(logitf0)
  f1 <- ilogit(logitf1)
  f2 <- ilogit(logitf2)
  S0 <- ilogit(logitS0)
  S1 <- ilogit(logitS1)
  S2 <- ilogit(logitS2)
  
  #Initialise theta vectors
  theta0 <- numeric(l)
  theta1 <- numeric(l)
  theta2 <- numeric(l)
  
  #Initialise row likelihood vectors
  lik0 <- numeric(l)
  lik1 <- numeric(l)
  lik2 <- numeric(l)
  
  #Create theta values
  for (i in l:1){
    if (i == l){
      theta0[i] <- f0[i]
      theta1[i] <- f1[i]
      theta2[i] <- f2[i]
    } else {
      theta0[i] <- f0[i] + S0[i]*theta0[i+1]
      theta1[i] <- f1[i] + S1[i]*theta0[i+1]
      theta2[i] <- f2[i] + S2[i]*theta1[i]
    }
  }
  
  eps <- 1e-12
  
  #Ensure theta values are valid
  theta0 <- pmin(pmax(theta0, eps), 1 - eps)
  theta1 <- pmin(pmax(theta1, eps), 1 - eps)
  theta2 <- pmin(pmax(theta2, eps), 1 - eps)
  
  ##Adult likelihood
  #Loop over rows
  for (p in 1:l){
    likAdult <- 0
    
    #Loop over columns
    for (i in p:l){
      
      #Main diagonal
      if (i == p){
        probAdult <- f0[i]
      } 
      
      #All others
      else {
        probAdult <- prod(S0[p:(i-1)]) * f0[i]
      }
      
      #Add value to likelihood according to log likelihood function
      likAdult <- likAdult + R[p, i]*log(probAdult)
    }
    
    #Add probability of non-recovery and assign to row likelihood
    lik0[p] <- likAdult + (N[p] - rowSums(R)[p]) * log(1 - theta0[p])
  }
  
  ##Juvenile likelihood
  #Loop over rows
  for (p in 1:l){
    likJuvenile <- 0
    
    #Loop over columns
    for (i in p:l){
      
      #Main diaongal
      if (i == p){
        probJuvenile <- f1[i]
      } 
      
      #One above main diaongal
      else if (i == p+1){
        probJuvenile <- S1[p]*f0[i]
      } 
      
      #All others
      else {
        probJuvenile <- S1[p] * prod(S0[(p+1):(i-1)]) * f0[i]
      }
      
      #Add value to likelihood according to log likelihood function
      likJuvenile <- likJuvenile + Q[p, i] * log(probJuvenile)
    }
    
    #Add probability of non-recovery and assign to row likelihood
    lik1[p] <- likJuvenile + (M[p] - rowSums(Q)[p]) * log(1 - theta1[p])
  }
  
  ##Pullus likelihood
  #Loop over rows
  for (p in 1:l){
    likPullus <- 0
    
    #Loop over columns
    for (i in p:(l+1)){
      
      #Main diagonal
      if (i == p){
        probPullus <- f2[i]
      } 
      
      #One above main diagonal
      else if (i == p+1){
        probPullus <- S2[p]*f1[p]
      } 
      
      #Two above main diagonal
      else if (i == p+2){
        probPullus <- S2[p]*S1[p]*f0[p+1]
      } 
      
      #All others
      else {
        probPullus <- S2[p] * S1[p] * prod(S0[(p+1):(i-2)]) * f0[i-1]
      }
      
      #Add value to likelihood according to log likelihood function
      likPullus <- likPullus + P[p, i] * log(probPullus)
    }
    
    #Add probability of non-recovery and assign to row likelihood
    lik2[p] <- likPullus + (L[p] - rowSums(P)[p]) * log(1 - theta2[p])
  }
  
  #Sum all row likelihoods
  totalLik0 <- sum(lik0)
  totalLik1 <- sum(lik1)
  totalLik2 <- sum(lik2)
  
  #Sum of age likelihoods
  totalLik <- totalLik0 + totalLik1 + totalLik2
  
  #Return negative as optim minimises
  -totalLik
}

likelihood3A_PFcomb <- function(params,
                                R, P, Q,
                                N, M, L){
  l <- dim(R)[1]
  
  #Parameters in logit space
  logitf0 <- params[1:l]
  logitf1 <- params[(l+1):(2*l)]
  logitf2 <- params[(2*l+1):(3*l)]
  logitS0 <- params[(3*l+1):(4*l-1)]
  logitS1 <- params[(4*l):(5*l-2)]
  logitS2 <- params[(5*l-1):(6*l-2)]
  
  #Parameters bounded between 0 and 1
  f0 <- ilogit(logitf0)
  f1 <- ilogit(logitf1)
  f2 <- ilogit(logitf2)
  S0 <- ilogit(logitS0)
  S1 <- ilogit(logitS1)
  S2 <- ilogit(logitS2)
  
  #Initialise theta vectors
  theta0 <- numeric(l)
  theta1 <- numeric(l)
  theta2 <- numeric(l)
  
  #Initialise row likelihood vectors
  lik0 <- numeric(l)
  lik1 <- numeric(l)
  lik2 <- numeric(l)
  
  #Create theta values
  for (i in l:1){
    if (i == l){
      theta0[i] <- f0[i]
      theta1[i] <- f1[i]
      theta2[i] <- f2[i]
    } else {
      theta0[i] <- f0[i] + S0[i]*theta0[i+1]
      theta1[i] <- f1[i] + S1[i]*theta0[i+1]
      theta2[i] <- f2[i] + S2[i]*theta1[i]
    }
  }
  
  eps <- 1e-12
  
  #Ensure theta values are valid
  theta0 <- pmin(pmax(theta0, eps), 1 - eps)
  theta1 <- pmin(pmax(theta1, eps), 1 - eps)
  theta2 <- pmin(pmax(theta2, eps), 1 - eps)
  
  ##Adult likelihood
  for (p in 1:l){
    likAdult <- 0
    
    for (i in p:l){
      if (i == p){
        probAdult <- f0[i]
      } else {
        probAdult <- prod(S0[p:(i-1)]) * f0[i]
      }
      
      likAdult <- likAdult + R[p, i]*log(probAdult)
    }
    
    lik0[p] <- likAdult + (N[p] - rowSums(R)[p]) * log(1 - theta0[p])
  }
  
  #Juvenile likelihood
  for (p in 1:l){
    likJuvenile <- 0
    
    for (i in p:l){
      if (i == p){
        probJuvenile <- f1[i]
      } else if (i == p+1){
        probJuvenile <- S1[p]*f0[i]
      } else {
        probJuvenile <- S1[p] * prod(S0[(p+1):(i-1)]) * f0[i]
      }
      
      likJuvenile <- likJuvenile + Q[p, i] * log(probJuvenile)
    }
    
    lik1[p] <- likJuvenile + (M[p] - rowSums(Q)[p]) * log(1 - theta1[p])
  }
  
  #Pullus likelihood
  for (p in 1:l){
    likPullus <- 0
    
    for (i in p:l){
      if (i == p){
        probPullus <- f2[i] + S2[i]*f1[i]
      } else if (i == p+1){
        probPullus <- S2[p]*S1[p]*f0[p+1]
      } else {
        probPullus <- S2[p] * S1[p] * prod(S0[(p+1):(i-1)]) * f0[i]
      }
      
      likPullus <- likPullus + P[p, i] * log(probPullus)
    }
    
    lik2[p] <- likPullus + (L[p] - rowSums(P)[p]) * log(1 - theta2[p])
  }
  
  totalLik0 <- sum(lik0)
  totalLik1 <- sum(lik1)
  totalLik2 <- sum(lik2)
  
  totalLik <- totalLik0 + totalLik1 + totalLik2
  
  -totalLik
}

nPar <- 118

inits <- logit(c(rep(0.02, l),
                 rep(0.02, l),
                 rep(0.02, l),
                 rep(0.7, l-1),
                 rep(0.6, l-1),
                 rep(0.5, l)))

resultSep <- optim(inits, likelihood3A_PFsep,
                   R = R, Q = Q, P = PSep, N = N, M = M, L = L,
                   method = "L-BFGS-B", hessian = TRUE)

resultComb <- optim(inits, likelihood3A_PFcomb,
                    R = R, Q = Q, P = PComb, N = N, M = M, L = L,
                    method = "L-BFGS-B", hessian = TRUE)


l <- dim(R)[1]

saveRDS(resultSep, "BrownieGLM_sep")
saveRDS(resultComb, "BrownieGLM_comb")

# resultSep  <- readRDS("BrownieGLM_sep")
# resultComb <- readRDS("BrownieGLM_comb")

#Function to pad vector x to length n
pad <- function(x, n) {
  length(x) <- n
  x
}

#Function to build dataframe of estimates, standard errors, and confidence interval bounds given optim result
getEstimsDf <- function(result){
  #Get estimates
  params <- result$par
  
  #Extract values
  logitf0 <- params[1:l]
  logitf1 <- params[(l+1):(2*l)]
  logitf2 <- params[(2*l+1):(3*l)]
  logitS0 <- params[(3*l+1):(4*l-1)]
  logitS1 <- params[(4*l):(5*l-2)]
  logitS2 <- params[(5*l-1):(6*l-2)]
  
  #Bound to (0, 1)
  f0 <- ilogit(logitf0)
  f1 <- ilogit(logitf1)
  f2 <- ilogit(logitf2)
  S0 <- ilogit(logitS0)
  S1 <- ilogit(logitS1)
  S2 <- ilogit(logitS2)
  
  #Calculate standard errors in logit space
  logitHess    <- result$hessian
  logitCov <- solve(logitHess)
  logitStdErrs <- sqrt(diag(logitCov))
  
  #Calculate 95% confidence interval bounds in logit space
  logitLower <- params - 1.96 * logitStdErrs
  logitUpper <- params + 1.96 * logitStdErrs
  
  #Bound 95% confidence interval bounds in bounded space
  lower <- ilogit(logitLower)
  upper <- ilogit(logitUpper)
  
  ilogitParams <- ilogit(params)
  
  #Calculate standard errors using delta method
  stdErrs <- logitStdErrs * ilogitParams * (1 - ilogitParams)
  
  #Extract standard errors
  f0StdErr <- stdErrs[1:l]
  f1StdErr <- stdErrs[(l+1):(2*l)]
  f2StdErr <- stdErrs[(2*l+1):(3*l)]
  S0StdErr <- stdErrs[(3*l+1):(4*l-1)]
  S1StdErr <- stdErrs[(4*l):(5*l-2)]
  S2StdErr <- stdErrs[(5*l-1):(6*l-2)]
  
  #Extract lower bounds
  f0Lower <- lower[1:l]
  f1Lower <- lower[(l+1):(2*l)]
  f2Lower <- lower[(2*l+1):(3*l)]
  S0Lower <- lower[(3*l+1):(4*l-1)]
  S1Lower <- lower[(4*l):(5*l-2)]
  S2Lower <- lower[(5*l-1):(6*l-2)]
  
  #Extract upper bounds
  f0Upper <- upper[1:l]
  f1Upper <- upper[(l+1):(2*l)]
  f2Upper <- upper[(2*l+1):(3*l)]
  S0Upper <- upper[(3*l+1):(4*l-1)]
  S1Upper <- upper[(4*l):(5*l-2)]
  S2Upper <- upper[(5*l-1):(6*l-2)]
  
  #Pad S0, S1 values to l (there are l-1 S0, S1 estiamtes)
  S0 <- pad(S0, l)
  S1 <- pad(S1, l)
  
  S0StdErr <- pad(S0StdErr, l)
  S1StdErr <- pad(S1StdErr, l)
  
  S0Lower <- pad(S0Lower, l)
  S1Lower <- pad(S1Lower, l)
  
  S0Upper <- pad(S0Upper, l)
  S1Upper <- pad(S1Upper, l)
  
  #Create dataframe
  GLMEstimsDf <- data.frame(time = 1:l,
                            f0Estim = f0, f1Estim = f1, f2Estim = f2,
                            S0Estim = S0, S1Estim = S1, S2Estim = S2,
                            f0StdErr, f1StdErr, f2StdErr,
                            S0StdErr, S1StdErr, S2StdErr,
                            f0Lower, f1Lower, f2Lower,
                            S0Lower, S1Lower, S2Lower,
                            f0Upper, f1Upper, f2Upper,
                            S0Upper, S1Upper, S2Upper)
  
  #Format dataframe
  df <- GLMEstimsDf %>%
    pivot_longer(-time,
                 names_to = c("Param", "Age", "Type"),
                 names_pattern = "([A-Za-z])([0-9])([A-Za-z]+)") %>%
    pivot_wider(names_from = Type,
                values_from = value)
  
  df
}

#Create dataframe from optim results
dfSep  <- getEstimsDf(resultSep) %>%
  mutate(Model = "Sep")
dfComb <- getEstimsDf(resultComb) %>%
  mutate(Model = "Comb")

#Combine dataframes
df <- rbind(dfSep, dfComb)]]>
</code>
</pre>
</details>

<details>
<summary>GLM Seber</summary>
<pre>
<code><![CDATA[
library(readxl)
library(dplyr)
library(tidyr)
library(purrr)
library(ggplot2)

setwd(dirname(parent.frame(2)$ofile))

windowsFonts(Font = windowsFont("CMU Serif"))

#Read data
adults    <- read_xlsx("../data/blackbirds/adults.xlsx")
juveniles <- read_xlsx("../data/blackbirds/juveniles.xlsx")
pulliSep  <- read_xlsx("../data/blackbirds/pulli.xlsx")
pulliComb <- read_xlsx("../data/blackbirds/pulliComb.xlsx")

#Select recovery counts
R     <- adults    %>% select(-c("Year of ringing", "Total ringed"))
Q     <- juveniles %>% select(-c("Year of ringing", "Total ringed"))
PSep  <- pulliSep  %>% select(-c("Year of ringing", "Total ringed"))
PComb <- pulliComb %>% select(-c("Year of ringing", "Total ringed"))

#Select totals ringed
N <- adults    %>% select("Total ringed")
M <- juveniles %>% select("Total ringed")
L <- pulliSep  %>% select("Total ringed")

#Convert recovery counts to matrix
R     <- as.matrix(R)
Q     <- as.matrix(Q)
PSep  <- as.matrix(PSep)
PComb <- as.matrix(PComb)

#Convert totals ringed to vectors
N <- as.vector(N)$`Total ringed`
M <- as.vector(M)$`Total ringed`
L <- as.vector(L)$`Total ringed`

#Inverse logit function
ilogit <- function(x){
  1/(1+exp(-x))
}

#Logit function
logit <- function(x){
  log(x / (1 - x))
}

#To calculate T_{i}, U_{i}
sumFunc <- function(data, i){
  rows <- rowSums(data)
  cols <- colSums(data)
  
  as.numeric(ifelse(i == 1, rows[1], sum(rows[1:i]) - sum(cols[1:(i-1)])))
}

#To calculate V_{i}
sumFuncPulli <- function(data, i){
  rows <- rowSums(data)
  cols <- colSums(data)
  
  as.numeric(ifelse(i == 1, rows[1], sum(rows[1:i]) - sum(cols[1:i]) + data[i,][i]))
}

#To calculate P_{.i}
colSumPulli <- function(data, i){
  out <- ifelse(i == l,
                data[i,][i] + sum(data[,(i+1)]),
                data[i,][i] + sum(data[,(i+1)]) - data[(i+1),][i+1])
  
  as.numeric(out)
}

#GLM likelihoods
likelihood3A_PFsep <- function(params,
                               R, P, Q,
                               N, M, L){
  l <- dim(R)[1]
  
  #Parameters in logit space
  logitr0 <- params[1:(l-1)]
  logitr1 <- params[l:(2*l-2)]
  logitr2 <- params[(2*l-1):(3*l-2)]
  logitS0 <- params[(3*l-1):(4*l-3)]
  logitS1 <- params[(4*l-2):(5*l-4)]
  logitS2 <- params[(5*l-3):(6*l-4)]
  
  logitf0l <- params[6*l-3]
  logitf1l <- params[6*l-2]
  
  #Parameters bounded between 0 and 1
  r0 <- ilogit(logitr0)
  r1 <- ilogit(logitr1)
  r2 <- ilogit(logitr2)
  S0 <- ilogit(logitS0)
  S1 <- ilogit(logitS1)
  S2 <- ilogit(logitS2)
  
  f0l <- ilogit(logitf0l)
  f1l <- ilogit(logitf1l)
  
  #Initialise theta vectors
  theta0 <- numeric(l)
  theta1 <- numeric(l)
  theta2 <- numeric(l)
  
  #Initialise row likelihood vectors
  lik0 <- numeric(l)
  lik1 <- numeric(l)
  lik2 <- numeric(l)
  
  #Create theta values
  for (i in l:1){
    if (i == l){
      theta0[i] <- f0l
      theta1[i] <- f1l
      theta2[i] <- (1 - S2[i]) * r2[i] + S2[i] * f1l #PF and Juv. probabilities
    } else {
      theta0[i] <- (1 - S0[i])*r0[i] + S0[i]*theta0[i+1]
      theta1[i] <- (1 - S1[i])*r1[i] + S1[i]*theta0[i+1]
      theta2[i] <- (1 - S2[i])*r2[i] + S2[i]*theta1[i]
    }
  }
  
  eps <- 1e-12
  
  #Ensure theta values are valid
  theta0 <- pmin(pmax(theta0, eps), 1 - eps)
  theta1 <- pmin(pmax(theta1, eps), 1 - eps)
  theta2 <- pmin(pmax(theta2, eps), 1 - eps)
  
  ##Adult likelihood
  #Loop over rows
  for (p in 1:l){
    likAdult <- 0
    
    #Loop over columns
    for (i in p:l){
      
      #Main diagonal
      if (i == p){
        if (i == l) {probAdult <- f0l}
        else        {probAdult <- (1 - S0[i])*r0[i]}
      }
      
      #All others
      else {
        if (i == l) {probAdult <- prod(S0[p:(i-1)]) * f0l}
        else        {probAdult <- prod(S0[p:(i-1)]) * (1 - S0[i])*r0[i]}
      }
      
      #Add value to likelihood according to log likelihood function
      likAdult <- likAdult + R[p, i]*log(probAdult)
    }
    
    #Add probability of non-recovery and assign to row likelihood
    lik0[p] <- likAdult + (N[p] - rowSums(R)[p]) * log(1 - theta0[p])
  }
  
  ##Juvenile likelihood
  #Loop over rows
  for (p in 1:l){
    likJuvenile <- 0
    
    #Loop over columns
    for (i in p:l){
      
      #Main diaongal
      if (i == p){
        if (i == l) {probJuvenile <- f1l}
        else        {probJuvenile <- (1 - S1[i]) * r1[i]}
      } 
      
      #One above main diaongal
      else if (i == p+1){
        if (i == l) {probJuvenile <- S1[p] * f0l}
        else        {probJuvenile <- S1[p] * (1 - S0[i]) * r0[i]}
      } 
      
      #All others
      else {
        if (i == l) {probJuvenile <- S1[p] * prod(S0[(p+1):(i-1)]) * f0l}
        else        {probJuvenile <- S1[p] * prod(S0[(p+1):(i-1)]) * (1 - S0[i]) * r0[i]}
      }
      
      #Add value to likelihood according to log likelihood function
      likJuvenile <- likJuvenile + Q[p, i] * log(probJuvenile)
    }
    
    #Add probability of non-recovery and assign to row likelihood
    lik1[p] <- likJuvenile + (M[p] - rowSums(Q)[p]) * log(1 - theta1[p])
  }
  
  ##Pullus likelihood
  #Loop over rows
  for (p in 1:l){
    likPullus <- 0
    
    #Loop over columns
    for (i in p:(l+1)){
      
      #Main diagonal - PF
      if (i == p){
        probPullus <- (1 - S2[i]) * r2[i]
      } 
      
      #One above main diagonal - JUV
      else if (i == p+1){
        if (i == (l+1)) {probPullus <- S2[p] * f1l}
        else            {probPullus <- S2[p] * (1 - S1[p]) * r1[p]}
      }
      
      #Two above main diagonal
      else if (i == p+2){
        if (i == (l+1)) {probPullus <- S2[p] * S1[p] * f0l}
        else            {probPullus <- S2[p] * S1[p] * (1 - S0[p+1])*r0[p+1]}
      } 
      
      #All others
      else {
        if (i == (l+1)) {probPullus <- S2[p] * S1[p] * prod(S0[(p+1):(i-2)]) * f0l}
        else            {probPullus <- S2[p] * S1[p] * prod(S0[(p+1):(i-2)]) * (1 - S0[i-1])*r0[i-1]}
      }
      
      #Add value to likelihood according to log likelihood function
      likPullus <- likPullus + P[p, i] * log(probPullus)
    }
    
    #Add probability of non-recovery and assign to row likelihood
    lik2[p] <- likPullus + (L[p] - rowSums(P)[p]) * log(1 - theta2[p])
  }
  
  #Sum all row likelihoods
  totalLik0 <- sum(lik0)
  totalLik1 <- sum(lik1)
  totalLik2 <- sum(lik2)
  
  #Sum of age likelihoods
  totalLik <- totalLik0 + totalLik1 + totalLik2
  
  #Return negative as optim minimises
  -totalLik
}

likelihood3A_PFcomb <- function(params,
                                R, P, Q,
                                N, M, L){
  l <- dim(R)[1]
  
  #Parameters in logit space
  logitr0 <- params[1:(l-1)]
  logitr1 <- params[l:(2*l-2)]
  logitr2 <- params[(2*l-1):(3*l-2)]
  logitS0 <- params[(3*l-1):(4*l-3)]
  logitS1 <- params[(4*l-2):(5*l-4)]
  logitS2 <- params[(5*l-3):(6*l-4)]
  
  logitf0l <- params[6*l-3]
  logitf1l <- params[6*l-2]
  
  #Parameters bounded between 0 and 1
  r0 <- ilogit(logitr0)
  r1 <- ilogit(logitr1)
  r2 <- ilogit(logitr2)
  S0 <- ilogit(logitS0)
  S1 <- ilogit(logitS1)
  S2 <- ilogit(logitS2)
  
  f0l <- ilogit(logitf0l)
  f1l <- ilogit(logitf1l)
  
  #Initialise theta vectors
  theta0 <- numeric(l)
  theta1 <- numeric(l)
  theta2 <- numeric(l)
  
  #Initialise row likelihood vectors
  lik0 <- numeric(l)
  lik1 <- numeric(l)
  lik2 <- numeric(l)
  
  #Create theta values
  for (i in l:1){
    if (i == l){
      theta0[i] <- f0l
      theta1[i] <- f1l
      theta2[i] <- (1 - S2[i]) * r2[i] + S2[i] * f1l #PF and Juv. probabilities
    } else {
      theta0[i] <- (1 - S0[i])*r0[i] + S0[i]*theta0[i+1]
      theta1[i] <- (1 - S1[i])*r1[i] + S1[i]*theta0[i+1]
      theta2[i] <- (1 - S2[i])*r2[i] + S2[i]*theta1[i]
    }
  }
  
  eps <- 1e-12
  
  #Ensure theta values are valid
  theta0 <- pmin(pmax(theta0, eps), 1 - eps)
  theta1 <- pmin(pmax(theta1, eps), 1 - eps)
  theta2 <- pmin(pmax(theta2, eps), 1 - eps)
  
  ##Adult likelihood
  #Loop over rows
  for (p in 1:l){
    likAdult <- 0
    
    #Loop over columns
    for (i in p:l){
      
      #Main diagonal
      if (i == p){
        if (i == l) {probAdult <- f0l}
        else        {probAdult <- (1 - S0[i])*r0[i]}
      }
      
      #All others
      else {
        if (i == l) {probAdult <- prod(S0[p:(i-1)]) * f0l}
        else        {probAdult <- prod(S0[p:(i-1)]) * (1 - S0[i])*r0[i]}
      }
      
      #Add value to likelihood according to log likelihood function
      likAdult <- likAdult + R[p, i]*log(probAdult)
    }
    
    #Add probability of non-recovery and assign to row likelihood
    lik0[p] <- likAdult + (N[p] - rowSums(R)[p]) * log(1 - theta0[p])
  }
  
  ##Juvenile likelihood
  #Loop over rows
  for (p in 1:l){
    likJuvenile <- 0
    
    #Loop over columns
    for (i in p:l){
      
      #Main diaongal
      if (i == p){
        if (i == l) {probJuvenile <- f1l}
        else        {probJuvenile <- (1 - S1[i]) * r1[i]}
      } 
      
      #One above main diaongal
      else if (i == p+1){
        if (i == l) {probJuvenile <- S1[p] * f0l}
        else        {probJuvenile <- S1[p] * (1 - S0[i]) * r0[i]}
      } 
      
      #All others
      else {
        if (i == l) {probJuvenile <- S1[p] * prod(S0[(p+1):(i-1)]) * f0l}
        else        {probJuvenile <- S1[p] * prod(S0[(p+1):(i-1)]) * (1 - S0[i]) * r0[i]}
      }
      
      #Add value to likelihood according to log likelihood function
      likJuvenile <- likJuvenile + Q[p, i] * log(probJuvenile)
    }
    
    #Add probability of non-recovery and assign to row likelihood
    lik1[p] <- likJuvenile + (M[p] - rowSums(Q)[p]) * log(1 - theta1[p])
  }
  
  ##Pullus likelihood
  #Loop over rows
  for (p in 1:l){
    likPullus <- 0
    
    #Loop over columns
    for (i in p:l){
      
      #Main diagonal - PF
      if (i == p){
        if (i == l) {probPullus <- (1 - S2[i]) * r2[i] + S2[i] * f1l}
        else        {probPullus <- (1 - S2[i]) * r2[i] + S2[i] * (1 - S1[i]) * r1[i]}
      } 
      
      #One above main diagonal - JUV
      else if (i == p+1){
        if (i == l) {probPullus <- S2[p] * S1[p] * f0l}
        else        {probPullus <- S2[p] * S1[p] * (1 - S0[p+1]) * r0[p+1]}
      }
      
      #All others
      else {
        if (i == l) {probPullus <- S2[p] * S1[p] * prod(S0[(p+1):(i-1)]) * f0l}
        else        {probPullus <- S2[p] * S1[p] * prod(S0[(p+1):(i-1)]) * (1 - S0[i])*r0[i]}
      }
      
      #Add value to likelihood according to log likelihood function
      likPullus <- likPullus + P[p, i] * log(probPullus)
    }
    
    #Add probability of non-recovery and assign to row likelihood
    lik2[p] <- likPullus + (L[p] - rowSums(P)[p]) * log(1 - theta2[p])
  }
  
  #Sum all row likelihoods
  totalLik0 <- sum(lik0)
  totalLik1 <- sum(lik1)
  totalLik2 <- sum(lik2)
  
  #Sum of age likelihoods
  totalLik <- totalLik0 + totalLik1 + totalLik2
  
  #Return negative as optim minimises
  -totalLik
}

#Get MLE means
getSepInits <- function(){
  dataSep <- data.frame(time = 1:l,
                        Ni  = adults$`Total ringed`,
                        Mi  = juveniles$`Total ringed`,
                        Li  = pulliSep$`Total ringed`,
                        Ri. = rowSums(R),
                        Qi. = rowSums(Q),
                        Pi. = rowSums(PSep),
                        R.i = colSums(R),
                        Q.i = colSums(Q),
                        P.i = map_dbl(1:l, ~colSumPulli(PSep, .x)),
                        Qii = diag(as.matrix(Q)),
                        Pii = diag(as.matrix(PSep)),
                        Pii1 = PSep[row(PSep) == col(PSep) - 1],
                        Ti  = map_dbl(1:l, ~sumFunc(R, .x)),
                        Ui  = map_dbl(1:l, ~sumFunc(Q, .x)),
                        Vi  = map_dbl(1:l, ~sumFuncPulli(PSep, .x))) %>%
    mutate(Ni1  = lead(Ni, order_by = row_number())) %>%
    mutate(Ri1. = lead(Ri., order_by = row_number())) %>%
    mutate(Zi = Ti + Ui - Qi. + Vi - Pi.) %>%
    mutate(Wi = R.i + Q.i - Qii + P.i - Pii - Pii1) %>%
    mutate(Ci = Qi. + Pi. - Pii) %>%
    mutate(Di = Qii + Pii1) %>%
    mutate(theta0 = Ri./Ni) %>%
    mutate(theta1 = Qi./Mi) %>%
    mutate(theta2 = Pi./Li)
  
  estimsSep <- dataSep %>%
    mutate(S0Estim = Ri./Ni * Ni1/Ri1. * (Zi - Wi)/Zi) %>%
    mutate(r0Estim = (Ri.*Wi*Ri1.) / (Ni*Ri1.*Zi - Ri.*Ni1*(Zi - Wi))) %>%
    mutate(S1Estim = Ni1/Ri1. * Qi./Mi * (Qi. - Qii + Pi. - Pii - Pii1)/(Qi. + Pi. - Pii)) %>%
    mutate(r1Estim = (Qi.*Di*Ri1.) / (Mi*Ri1.*Ci - Qi.*Ni1*(Ci - Di))) %>%
    mutate(S2Estim = Mi/Li * (Pi. - Pii)/Qi.) %>%
    mutate(r2Estim = Pii*Qi. / (Li*Qi. - Mi*(Pi. - Pii)))
  
  boundProb <- function(x) {min(max(x, 0), 1)}
  
  means <- estimsSep %>%
    summarise(meanr0 = mean(sapply(na.omit(r0Estim), boundProb)),
              meanr1 = mean(sapply(na.omit(r1Estim), boundProb)),
              meanr2 = mean(sapply(na.omit(r2Estim), boundProb)),
              meanS0 = mean(sapply(na.omit(S0Estim), boundProb)),
              meanS1 = mean(sapply(na.omit(S1Estim), boundProb)),
              meanS2 = mean(sapply(na.omit(S2Estim), boundProb)))

  means
}

l <- dim(R)[1]

nPar <- 6*l - 2

initMeans <- getSepInits()

r0Mean <- initMeans$meanr0
r1Mean <- initMeans$meanr1
r2Mean <- initMeans$meanr2

S0Mean <- initMeans$meanS0
S1Mean <- initMeans$meanS1
S2Mean <- initMeans$meanS2

inits <- logit(c(rep(0.05, l-1),
                 rep(0.05, l-1),
                 rep(0.05, l),
                 rep(0.7, l-1),
                 rep(0.6, l-1),
                 rep(0.5, l),
                 0.02,
                 0.02))

resultSep <- optim(inits, likelihood3A_PFsep,
                   R = R, Q = Q, P = PSep, N = N, M = M, L = L,
                   method = "BFGS", hessian = TRUE)

resultComb <- optim(inits, likelihood3A_PFcomb,
                    R = R, Q = Q, P = PComb, N = N, M = M, L = L,
                    method = "BFGS", hessian = TRUE)

saveRDS(resultSep,  "SeberGLM_sep")
saveRDS(resultComb, "SeberGLM_comb")

# resultSep  <- readRDS("SeberGLM_sep")
# resultComb <- readRDS("SeberGLM_comb")

l <- dim(R)[1]

#Functions to pad vector x to length n
padRight <- function(x, n){
  length(x) <- n
  x
}

padLeft <- function(x, n){
  out <- rep(NA, n)
  out[(n - length(x) + 1):n] <- x
  
  out
}

#Function to build dataframe of estimates, standard errors, and confidence interval bounds given optim result
getEstimsDf <- function(result){
  #Get estimates
  params <- result$par
  
  #Parameters in logit space
  logitr0 <- params[1:(l-1)]
  logitr1 <- params[l:(2*l-2)]
  logitr2 <- params[(2*l-1):(3*l-2)]
  logitS0 <- params[(3*l-1):(4*l-3)]
  logitS1 <- params[(4*l-2):(5*l-4)]
  logitS2 <- params[(5*l-3):(6*l-4)]
  
  logitf0l <- params[6*l-3]
  logitf1l <- params[6*l-2]
  
  #Parameters bounded between 0 and 1
  r0 <- ilogit(logitr0)
  r1 <- ilogit(logitr1)
  r2 <- ilogit(logitr2)
  S0 <- ilogit(logitS0)
  S1 <- ilogit(logitS1)
  S2 <- ilogit(logitS2)
  
  f0l <- ilogit(logitf0l)
  f1l <- ilogit(logitf1l)
  
  #Calculate standard errors in logit space
  logitHess    <- result$hessian
  logitCov <- solve(logitHess)
  logitStdErrs <- sqrt(diag(logitCov))
  
  #Calculate 95% confidence interval bounds in logit space
  logitLower <- params - 1.96 * logitStdErrs
  logitUpper <- params + 1.96 * logitStdErrs
  
  #Bound 95% confidence interval bounds in bounded space
  lower <- ilogit(logitLower)
  upper <- ilogit(logitUpper)
  
  ilogitParams <- ilogit(params)
  
  #Calculate standard errors using delta method
  stdErrs <- logitStdErrs * ilogitParams * (1 - ilogitParams)
  
  #Extract standard errors
  r0StdErr <- stdErrs[1:(l-1)]
  r1StdErr <- stdErrs[l:(2*l-2)]
  r2StdErr <- stdErrs[(2*l-1):(3*l-2)]
  S0StdErr <- stdErrs[(3*l-1):(4*l-3)]
  S1StdErr <- stdErrs[(4*l-2):(5*l-4)]
  S2StdErr <- stdErrs[(5*l-3):(6*l-4)]
  
  f0lStdErr <- stdErrs[6*l-3]
  f1lStdErr <- stdErrs[6*l-2]
  
  #Extract lower bounds
  r0Lower <- lower[1:(l-1)]
  r1Lower <- lower[l:(2*l-2)]
  r2Lower <- lower[(2*l-1):(3*l-2)]
  S0Lower <- lower[(3*l-1):(4*l-3)]
  S1Lower <- lower[(4*l-2):(5*l-4)]
  S2Lower <- lower[(5*l-3):(6*l-4)]
  
  f0lLower <- lower[6*l-3]
  f1lLower <- lower[6*l-2]
  
  #Extract upper bounds
  r0Upper <- upper[1:(l-1)]
  r1Upper <- upper[l:(2*l-2)]
  r2Upper <- upper[(2*l-1):(3*l-2)]
  S0Upper <- upper[(3*l-1):(4*l-3)]
  S1Upper <- upper[(4*l-2):(5*l-4)]
  S2Upper <- upper[(5*l-3):(6*l-4)]
  
  f0lUpper <- upper[6*l-3]
  f1lUpper <- upper[6*l-2]
  
  #Pad S0, S1, r0, r1 values to l (there are l-1 S0, S1, r0, r1 estimates)
  S0 <- padRight(S0, l)
  S1 <- padRight(S1, l)
  r0 <- padRight(r0, l)
  r1 <- padRight(r1, l)
  
  S0StdErr <- padRight(S0StdErr, l)
  S1StdErr <- padRight(S1StdErr, l)
  r0StdErr <- padRight(r0StdErr, l)
  r1StdErr <- padRight(r1StdErr, l)
  
  S0Lower <- padRight(S0Lower, l)
  S1Lower <- padRight(S1Lower, l)
  r0Lower <- padRight(r0Lower, l)
  r1Lower <- padRight(r1Lower, l)
  
  S0Upper <- padRight(S0Upper, l)
  S1Upper <- padRight(S1Upper, l)
  r0Upper <- padRight(r0Upper, l)
  r1Upper <- padRight(r1Upper, l)
  
  #Pad f0l, f1l values to l (there are 1 f0l, f1l estimates)
  f0l <- padLeft(f0l, l)
  f1l <- padLeft(f0l, l)
  
  f0lStdErr <- padLeft(f0lStdErr, l)
  f1lStdErr <- padLeft(f1lStdErr, l)
  
  f0lLower <- padLeft(f0lLower, l)
  f1lLower <- padLeft(f1lLower, l)
  
  f0lUpper <- padLeft(f0lUpper, l)
  f1lUpper <- padLeft(f1lUpper, l)
  
  #Create dataframe
  GLMEstimsDf <- data.frame(time = 1:l,
                            r0Estim = r0, r1Estim = r1, r2Estim = r2,
                            S0Estim = S0, S1Estim = S1, S2Estim = S2,
                            f0Estim = f0l, f1Estim = f1l,
                            r0StdErr, r1StdErr, r2StdErr,
                            S0StdErr, S1StdErr, S2StdErr,
                            f0StdErr = f0lStdErr, f1StdErr = f1lStdErr,
                            r0Lower, r1Lower, r2Lower,
                            S0Lower, S1Lower, S2Lower,
                            f0Lower = f0lLower, f1Lower = f1lLower,
                            r0Upper, r1Upper, r2Upper,
                            S0Upper, S1Upper, S2Upper,
                            f0Upper = f0lUpper, f1Upper = f1lUpper)
  
  #Format dataframe
  df <- GLMEstimsDf %>%
    pivot_longer(-time,
                 names_to = c("Param", "Age", "Type"),
                 names_pattern = "([A-Za-z])([0-9])([A-Za-z]+)") %>%
    pivot_wider(names_from = Type,
                values_from = value)

  df
}

#Create dataframe from optim results
dfSep  <- getEstimsDf(resultSep) %>%
  mutate(Model = "Sep")
dfComb <- getEstimsDf(resultComb) %>%
  mutate(Model = "Comb")

df <- rbind(dfSep, dfComb)]]>
</code>
</pre>
</details>

<details>
<summary>RW1 and RW2</summary>
<pre>
<code><![CDATA[
library(readxl)
library(dplyr)
library(tidyr)
library(tibble)
library(stringr)
library(ggplot2)
library(runjags)

setwd(dirname(parent.frame(2)$ofile))

windowsFonts(Font = windowsFont("CMU Serif"))

#Read data
adults    <- read_xlsx("../data/blackbirds/adults.xlsx")
juveniles <- read_xlsx("../data/blackbirds/juveniles.xlsx")
pulliSep  <- read_xlsx("../data/blackbirds/pulli.xlsx")

#Select totals ringed
N <- adults    %>% select("Total ringed") %>% pull
M <- juveniles %>% select("Total ringed") %>% pull
L <- pulliSep  %>% select("Total ringed") %>% pull

#Select recovery counts
R     <- adults    %>% select(-c("Year of ringing", "Total ringed"))
Q     <- juveniles %>% select(-c("Year of ringing", "Total ringed"))
PSep  <- pulliSep  %>% select(-c("Year of ringing", "Total ringed"))

R     <- cbind(R,     end = N - rowSums(R))
Q     <- cbind(Q,     end = M - rowSums(Q))
PSep  <- cbind(PSep,  end = L - rowSums(PSep))

#Convert recovery counts to matrix
R     <- as.matrix(R)
Q     <- as.matrix(Q)
PSep  <- as.matrix(PSep)

#Bundle data
data_sep <- list(marr.p = PSep, marr.j = Q, marr.a = R, 
                 rel.p  = L,    rel.j = M,  rel.a = N,
                 n.occasions = dim(R)[2]-1)

l <- dim(R)[1]

#Initial values
inits <- function(){list(logit_sp = runif(l, 0, 1),
                         logit_sj = runif(l, 0, 1),
                         logit_sa = runif(l, 0, 1),
                         logit_rp = runif(l, 0, 1),
                         logit_rj = runif(l, 0, 1), 
                         logit_ra = runif(l, 0, 1))}

#Parameters monitored
parameters <- c("sp", "rp", "sj", "rj", "sa", "ra",
                "tau_sp", "tau_rp", "tau_sj", "tau_rj", "tau_sa", "tau_ra")

#MCMC settings
ni <- 50000
nt <- 10
nb <- 10000 
nc <- 3

# Create JAGS model
sepRW1Model <- run.jags(model = read.jagsfile("../JAGS_models/3a_TV_RW1_sep.txt"),
                        data = data_sep, 
                        inits = inits, 
                        monitor = parameters,
                        n.chains = nc, 
                        thin = nt,
                        sample = ni-nb,
                        burnin = nb,
                        adapt = 2000,
                        method = "parallel")

sepRW2Model <- run.jags(model = read.jagsfile("../JAGS_models/3a_TV_RW2_sep.txt"),
                        data = data_sep, 
                        inits = inits, 
                        monitor = parameters,
                        n.chains = nc, 
                        thin = nt,
                        sample = ni-nb,
                        burnin = nb,
                        adapt = 2000,
                        method = "parallel")

saveRDS(sepRW1Model, "sepRW1Model")
saveRDS(sepRW2Model, "sepRW2Model")

# sepRW1Model <- readRDS("sepRW1Model")
# sepRW2Model <- readRDS("sepRW2Model")

mcmcSepRW1    <- sepRW1Model$mcmc
resultsSepRW1 <- as.data.frame(do.call(rbind, mcmcSepRW1))

mcmcSepRW2    <- sepRW2Model$mcmc
resultsSepRW2 <- as.data.frame(do.call(rbind, mcmcSepRW2))

getSummary <- function(results){
  results <- results %>%
    {for (i in 1:20) {
      .[[paste0("fa[", i, "]")]] <- (1 - .[[paste0("sa[", i, "]")]]) * .[[paste0("ra[", i, "]")]]
    }
      .
    } %>%
    {for (i in 1:20) {
      .[[paste0("fj[", i, "]")]] <- (1 - .[[paste0("sj[", i, "]")]]) * .[[paste0("rj[", i, "]")]]
    }
      .
    } %>%
    {for (i in 1:20) {
      .[[paste0("fp[", i, "]")]] <- (1 - .[[paste0("sp[", i, "]")]]) * .[[paste0("rp[", i, "]")]]
    }
      .
    }
  
  summary <- results %>%
    select(!starts_with("tau")) %>%
    summarise(across(everything(), list(
      mean = ~mean(.),
      sd = ~sd(.),
      lower = ~quantile(., 0.025),
      upper = ~quantile(., 0.975)
    ))) %>%
    pivot_longer(
      cols = everything(),
      names_to = c("paramAgeYear", "stat"),
      names_pattern = "^(.*)_(mean|sd|lower|upper)$",
      values_to = "value"
    )%>%
    pivot_wider(
      names_from = stat,
      values_from = value
    ) %>%
    mutate(param = substr(paramAgeYear, 1, 1),
           age   = substr(paramAgeYear, 2, 2),
           year  = as.integer(sub(".*\\[(\\d+)\\]", "\\1", paramAgeYear))) %>%
    select(year, param, age, mean, sd, lower, upper)
  
  summary
}

summSepRW1 <- getSummary(resultsSepRW1)
summSepRW2 <- getSummary(resultsSepRW2)

summSepRW1 <- summSepRW1 %>% mutate(model = "sepRW2")
summSepRW2 <- summSepRW2 %>% mutate(model = "sepRW1")

summ <- rbind(summSepRW1, summSepRW2)

#Function to get sigma estimates
tauToSigmaSumm <- function(results){
  results %>% 
    select(starts_with("tau")) %>%
    mutate(across(everything(),
                  function(x){1/sqrt(x)},
                  .names = "sigma_{sub('^tau_', '', .col)}")) %>%
    select(starts_with("sigma")) %>%
    pivot_longer(cols = everything(),
                 names_to = "paramAge") %>%
    mutate(param = substr(paramAge, 7, 7),
           age   = substr(paramAge, 8, 8)) %>%
    select(-paramAge) %>%
    group_by(param, age) %>%
    summarise(Mean   = mean(value),
              Median = median(value),
              StdErr = sd(value),
              Lower  = quantile(value, 0.025),
              Upper  = quantile(value, 0.975))
}

sigmaSummRW1 <- tauToSigmaSumm(resultsSepRW1)
sigmaSummRW2 <- tauToSigmaSumm(resultsSepRW2)]]>
</code>
</pre>
</details>

<style>
  body{
    margin-bottom: 100px;
  }
</style>