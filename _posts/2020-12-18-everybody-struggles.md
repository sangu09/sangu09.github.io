---
layout: post
title: "Episode 1: Everybody struggles"
---

<p>It is the third week of the outreachy and the topic for the blog is everybody struggles.</p>

<p>Developers deal with technical problems all day long and you learn very fast that not only do you need to have different problem-solving skills but that it’s also just as important to know when you need to ask for help </p>

> “ Don't be afraid to ask questions. Don't be afraid to ask for help when you need it. I do that every day. Asking for help isn't a sign of weakness, it's a sign of strength. It shows you have the courage to admit when you don't know something, and to learn something new.” ~ Barack Obama

<p>My work in git is to accelerate rename detection so that merging of two branches becomes faster But now Elijah is working on a new merge-ort facility and he says that rename detection is no longer the rate determining step in most cases. So this week I had to work on making the performance tests to benchmark the merge-ort and to search for some special cases where accelerating rename detection can help but I feel that I spoiled the whole week because of a silly mistake caused by the OS and after three weeks I don’t even have a single line of code merged to the git codebase. <i>(Well I am working on having my first patch merged, it is left on some reviews)</i></p>

<p>Speaking about the silly mistake, I had a basic performance test failing on my OS X. Initially I thought that there was some mistake with cloning the repository or my setup etc and I checked thoroughly for all the things but nothing worked. After discussions with mentors, it came out that it wasn’t because of my setup, it was a general mac os issue which caused the basic test to fail. There is a function called test_export() which wasn’t able to export a variable. This was because the macOS uses the BSD version of sed, which differs in many respects from the GNU sed version that comes with Linux distros.  There was a solution in almost 3-4 hours on adding that issue to the mailing list. </p>

<p> So now I can finally start writing the code to benchmark the merge-ort. </p>
