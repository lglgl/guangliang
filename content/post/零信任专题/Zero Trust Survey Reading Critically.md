---
title: Zero Trust Survey Reading Critically
tags: [零信任]
id: '710'
categories: ["信息安全"]
date: 2023-12-29T01:11:48+08:00
url: "Zero-Trust-Survey-Reading-Critically.html"
---

## 1. Abstract

- Traditional network defense is boundary-based and no longer suitable for current technological developments.
- Zero Trust is a security model that can meet the demands of new developments.
- Understanding of Zero Trust is still in its early stages.
- Introduce the Zero Trust architecture, compare different technologies, and summarize the advantages and disadvantages of various solutions.

## 2. Introduction

- Security is of paramount importance, with traditional security relying on perimeter defense.
- Once malicious attacks breach the internal defenses, there is a potential for gaining prolonged trust, posing extremely high risks.
- The evolution of the Internet of Things (IoT) and cloud computing has led to changes in the structure and requirements of security, rendering the original framework inadequate for developmental needs.
- The concept of zero trust, as introduced by NIST, has undergone initial implementation.
- Trust necessitates a two-step process involving authentication and authorization.
- Contributions of the article:
    - Summarizing the current state of zero trust in architecture, identity authentication, access control, and trust evaluation algorithms.
    - Comparing technologies within the aforementioned four dimensions, identifying strengths and major issues.
    - Summarizing challenges faced and proposing future research directions.

## 3. Related Work

- I haven't delved into other literature about ZTA, but I believe the current urgent applications of zero trust may lie in two directions: the Internet of Things (IoT) and cloud computing.

## 4. Methodology/Result

- a. Case Studies:
    - References several cases, such as Google's BeyondCorp model, to illustrate practical applications and effectiveness of the zero trust model.
- b. Comparative Analysis:
    - Conducted on core technologies, including identity authentication, access control, and trust evaluation algorithms.
    - Comparison made for each technology's main solutions to summarize advantages and disadvantages.
- c. Problem Analysis:
    - Points out that the application of zero trust is still in its infancy, and there is a lack of complete understanding among enterprises, organizations, and individuals regarding the advantages and disadvantages of zero trust.

## 5. Discussion (Findings)

- a. Weakness:
    - Two-way authentication security scheme for IoT based on DTLS protocol has a higher computational cost and storage space.
    - Weever et al. [8] proposed a zero trust network security model, but it does not implement behavioral analysis and data leakage detection.
    - In 2016, DeCusatis et al. [5] proposed a zero trust method based on transport access control, providing protection at layer 3/4 but not at layer 7.
- b. Strength:
    - As early as 2018, Jayasinghe U and others [46] applied machine learning technology to the node trust evaluation framework.

## 6. Suggestions for Improvement

- a. Focus on the direction of identity authentication in future zero trust architecture.
- b. Consider the application of machine learning technology.
- c. Improve the accuracy of dynamic comprehensive assessments, especially in private networks or specific industries.
