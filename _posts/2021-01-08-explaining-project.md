---
layout: post
title: "Episode 2: Explaining my project"
---

Hey everyone! Happy New Year! I hope you are enjoying your vacation.

In this blog, I would be explaining my project in layman’s terms. I would try to include the explanations for Open Source Softwares and contributions, Git and then I would finally explain my project. 

Starting with Open source software, it is software that anyone can inspect, or modify as per his/her needs or for the enhancement of the community. An open-source community is a community of developers contributing to the software by ways including but not limited to coding, documenting, reviewing, designing, or even using the codebase. Through this community, developers can get inspiration and support from many like-minded people. It is a community where people learn and grow together. 

Briefing about Git, Git is a version control system(if put too simply: a tool that can allow one undo or redo any changes across single or multiple files) that keeps track of your source code and the changes made to it over time. Git can help us determine who changed the file, when it was changed, and supposedly why was it changed. In large and complex projects, where multiple people work simultaneously it might be common to get into a weird state without any version control system. A version control system allows one to branch out a feature and make changes to that branch and finally, when the feature is completed, request to add that feature in the project. Git isn’t the only version control system but it is surely the most popular. Every developer contributing to any project might have encountered Git at least once in his/her career. 

Now, being a version control system, Git has to keep track of all the changes including any rename changes that might have taken place. To detect renames, Git creates a matrix containing the similarity percentage between m and n files present on both sides. Therefore it has to perform m*n operations if there are m and n files on source and target. This m and n might be very huge sometimes and therefore rename detection can even take hours to complete. Therefore my problem statement is to accelerate that rename detection. 

Some ways to accelerate that rename detection might be to: 
1. If an exact file is detected we can skip the operations required for that files. 
2. If the source and destination files have the same name but different directory structure then check for it first and only continue if they are not perfect matches. 

Elijah Newren is working on these changes in his merge-ort work. He has also made a presentation explaining his changes [here](https://github.com/newren/presentations/blob/pdfs/merge-performance/merge-performance-slides.pdf) which is a great source if anyone wants to understand the merge machinery and his improvements. 

Another possible way to accelerate rename detection might be to first preprocess the file contents and then try to find correspondences for possible renames in a more guided manner on the processed items by using some sort of nearest neighbor search algorithms which I would be working on. As Elijah is already working on accelerating the rename detection, the first steps in my project are to check the performance achieved by his work and see if there are cases where further improvements might still be helpful. 

Well that's it! Easy? I dunno! xD
