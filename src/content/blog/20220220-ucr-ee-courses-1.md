---
title: UCR EE courses website
pubDatetime: 2022-02-20T16:00:00Z
postSlug: ucr-ee-courses-website-1
featured: false
draft: false
tags:
  - project
description: It's time to register for some classes to take in the Spring
  quarter. Unfortunately, course selection has never been a pleasant experience
  at UCR, because UCR's course selection system is terrible.
---
It's time to register for some classes to take in the Spring quarter. Unfortunately, course selection has never been a pleasant experience at UCR, because UCR's course selection system is terrible. Besides, as an Electrical Engineering student, I became confused about the prerequisites for certain classes. That's why I wanted to make a course roadmap that can:

1. Give an intuitive understanding of what classes should be taken as an EE major student.
2. Spot out the prerequisites of a class.
3. Be used as a tool to build a functioning course plan.

## Diagrams.net

My first idea is to manually search for course information, then draw out a huge flowchart. It turned out that drawing using [diagrams.net](https://diagrams.net) isn't not a bad idea at all, because it gives me a lot of freedom in terms of how I want to format things, so the roadmap produced is aesthetically pleasing.  
Here's how it looks like:

![diagrams.net roadmap](https://i1.lensdump.com/i/rSURL0.png)

But, there are also a few drawbacks:

1. Manually drawing the roadmap is time consuming.
2. I need to constantly think about how to rearrange the order of nodes to make the entire roadmap look better.
3. There is no possibility that this approach can be automated.

## Mermaid JS

So, I tried out [Mermaid JS](https://github.com/mermaid-js/mermaid). Mermaid JS is a graph making tool that can be used either locally or on a website. Recently, Github also integrated Mermaid into Markdown files, so people could easily produce graphs using code.

Mermaid is helpful in many ways. Because graphs can be generated using code, automation should be possible. Mermaid also supports adding events to nodes, which makes interactivity possible.

So far, I've only made a very crude prototype on [https://sonnyding1.github.io/UCR-EE-courses/](https://sonnyding1.github.io/UCR-EE-courses/). The webpage is essentially just an SVG image at this moment. If I were to continue enhancing this webpage, then I need to:

- Add an "info" button that describes what the colors and arrows mean.
- Add mouse click event to each node such that if one node is clicked, all previous nodes are highlighted.
- Research on better zoom methods and better scroll methods.
- Give users the option to choose from one of the six branches, and change the graph accordingly.

But the more I worked with Mermaid, the more I started to realize that Mermaid is made for small and static sketches, not for huge flowcharts that have interactivity. Sure, Mermaid supports adding events to nodes, but the best usage of that is probably adding links to nodes. Mermaid is stiff, it doesn't allow me to change the location of nodes at ease. This is problematic, because it means I need to put in a lot of time to "untangle" the links. In that case, I might be better drawing the roadmap manually.

## Conclusion

Anyways, if you are also an EE major student at UCR, you can still use the roadmap I made as a reference.

Usage:  
Purple nodes are major required classes, white nodes are not required classes, one of the two grey classes are required, and red nodes are classes that fulfill different focus areas' requirements. A student only needs to choose two classes from their focus area.  
The six focus areas are CSPN (Communications, Signal Processing and Networking), CR (Control and Robotics), VLSI (Embedded Systems and VLSI), IS (Intelligent Systems), NMDC (Nanotechnology, Advanced Materials, and Devices), PSSM (Power Systems and Smart Grid).  
Solid lines signify "prerequisite", dashed lines signify "can be taken concurrently".

If there are any mistakes, please point it out. Thank you in advance.

Next time, unless I've found a tool that can fulfill my needs, I will build this website from scratch.
