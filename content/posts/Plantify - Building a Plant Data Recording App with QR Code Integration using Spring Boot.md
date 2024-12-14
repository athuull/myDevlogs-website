---
title: "Plantify - Building a Plant Data Recording App with QR Code Integration using Spring Boot"
date: 2024-12-14
draft: true
tags: ["Java", "SpringBoot", "Thymeleaf", "PostgreSQL"]
---

I had been tasked with assigning QR codes to some plants, which when scanned, should lead to a web page with its name and some facts regarding that plant. At first, since there were only a handful of plants to work with, I built static web pages for each plant and generated QR codes that encoded the URLs to these static pages. 

This worked for a small number of plants, but when my college asked me to scale it up to include all the plants on the campus, I knew I had to come up with something more scalable. Perhaps an app that could dynamically create a web page along with a QR code—leading to that page by taking in user input—could solve the problem.

Here's how I broke the problem down:

1. **HTML Form for User Input:** I created a static HTML page where users could input plant data, such as the plant's name, its scientific name, description, and any interesting facts.
   
2. **Database to Store Plant Data:** I used PostgreSQL as the database to store the plant information.

3. 3. **Spring Boot Backend:** I built a backend using Spring Boot to handle user input and store the data into the database. Additionally, the backend fetches stored data from the database and serves it to the front-end.

4. **Dynamic Web Pages with Thymeleaf:** Using Thymeleaf as the template engine, I fetched the stored data from the database to generate dynamic web pages for each plant. These pages display the plant's details in a user-friendly layout.

5. **QR Code Generation:** I included an embedded JavaScript code that utilized a QR code generation API. This code encoded the generated dynamic URL for the plant page and displayed the QR code directly on the static web page. Users could then download the QR code for their use.

By integrating these components, the application allows seamless addition of new plant records and automatic generation of web page and QR codes for each plant.

This project not only solved the scaling problem but also provided a reusable system for managing plant data and generating QR codes efficiently.

Source code for the app : https://github.com/athuull/plantify
