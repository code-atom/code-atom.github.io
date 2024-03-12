---
title: Shift Left in Agile/DevOps Development
date: 2024-03-12 13:20:00 +05:30
---

### ***Preface***

> As the demand to release quality software products in a short amount of time continues to accelerate, Agile and DevOps teams have to adopt new approaches to avoid falling behind. Continuous testing and shift left testing are two breakthrough solutions for them in the modern era of software delivery.
>
> ***Dev**: Reposition the testing phase closer to the build delivery process.*
>
> ***Tester**: Performing testing in earlier development cycle to reduce bugs.*

### **Shift Left**

Traditional software development model is based on requirement engineering takes place on the left side of the project planning while delivery and testing requirements are on the right side. The model is not very flexible with changing client’s expectations and requirements and leads to negative outcomes for the business such as increased costs, increased time to market, and unexpected errors.

![Untitled.png](/uploads/Untitled.png)

Shift Left Testing literally pushes testing to the “left,” i.e., to earlier stages in the pipeline. The Shift Left approach intends to identify and resolve bugs as early as possible in the development process. This improves software quality and reduces time spent in resolving issues (which usually magnifies as development progress) later in the pipeline.

![Untitled 1.png](/uploads/Untitled%201.png)

![Untitled 2.png](/uploads/Untitled%202.png)

Shift Left is core testing concept of the Agile DevOps methodology, which speed up application development by release small builds frequently  as code changes.

Shift left is all about shifting testing of the software to an early phase in the SDLC. **Test automation** and **DevOps infrastructure** are important ingredients to achieve success in increasing efficiency and quality.

Shift Left places strong emphasis on conducting testing activities earlier in the development process . It is a proactive testing approach based on the idea that the earlier defects are identified and resolved, preventing them from propagating across the entire application, the lower the cost and effort required for fixing.

Key elements of Shift Left Testing:

* Early Testing (Bugs less expensive and easier to fix)

* Collaborative Approach

* Automation

Shift Left support different type of models in software development. For more details on this model

### **Agile Shift Left Model**

[Agile](https://en.wikipedia.org/wiki/Agile_software_development) and [DevOps](https://en.wikipedia.org/wiki/DevOps) projects are characterized by multiple short-duration sprints, as opposed to a single or a small number of longer phases, such as in shift-left testing. These shorter periods may be adjusted if early sprints are dedicated to defining basic requirements and architecture or implementing test-first and [test-driven development](https://en.wikipedia.org/wiki/Test-driven_development) (TDD). The concept of "shift-left" arises from the fact that the types of testing performed during the initial stages of these smaller sprints precede those conducted during later stages of the larger sprint(s) they replace.

![Untitled 3.png](/uploads/Untitled%203.png)

While Agile and DevOps share similar features, Agile testing typically **focuses on developmental testing rather than operational testing**, which takes place after the system is deployed.

### **Why?**

### **Cost Effective**

When testing is paused until the end of development, any bugs that do show up will usually be more difficult to fix. Their resolution needs the software to be reworked in its entirety since all the code has already been created. Consequences of such an approach would be:

* Increase in cost, since the software needs to be sent back and reworked from scratch

* Increased time to market since such reworks will take longer to complete

When it comes to fixing bugs, the earlier, the cheaper. According to the [NIST](https://www.nist.gov/), [resolving defects in production can cost 30 times more and up to 60 times more in case of security defects](https://www.vorto.co/blog/2020/2/27/reduce-cost-defects-service-now#:\~:text=The%20relative%20cost%20of%20fixing,in%20case%20of%20security%20defects).

![Untitled 4.png](/uploads/Untitled%204.png)

The Shift Left Testing approach implements a process that lets developers [find bugs](https://www.browserstack.com/guide/how-to-find-bugs-in-software) early and often. Bugs identified when code is being written or reviewed by devs are the easiest to fix because these code units are small and infinitely more manageable. Once merged into the main development branch, the breadth of code widens, and so does the effort required to find anomalies within it.

### **Increase Team Velocity and Quality**

Since bugs could occur at any iteration of software development, it makes sense to also incorporate testing along the way to catch them and prevent further problems as soon as possible. Combining continuous testing and shift left testing is a great strategy to improve software quality, meet users’ expectations, optimize the allocated resources, and minimize risks of bug-breaking apps from slipping into production.

### **How?**

Shifting left requires two key practices – **continuous testing** and **continuous deployment.**

* **Continuous testing** is the process of executing automated tests as early as possible, along with service virtualization to mimic unavailable systems. Continuous testing establishes a constant feedback loop from the testing team to the developers. It ensures prompt bug identification and early bug detection. The entire software development process is therefore more optimized at every stage. Continuous testing relies quite heavily on automation.

* **Continuous deployment**, the deployment pipelines are completely automated, and they have triggered automatically for every change or at an interval.

### **Risks of Shift Left**

With the availability of powerful automation tools, there's a temptation to apply all kinds of testing to every line of code. However, this approach is risky.

While testing side effects such as verifying if a record was saved in the database may seem appealing, it's important to note that testing implementation details is considered an anti-pattern due to its high susceptibility to breakage. These tests often require frequent modifications whenever changes are made to the application. Similarly, UI tests fall into this category since they focus on implementation specifics.

Verification tests just care about the “what,” not the “how” or the “why.” Ideally, the user requirements have been designed to validate “why.” To answer “how”, we can rely on a more powerful automation in the form of an observability platform.

**Reference**

[https://dzone.com/articles/shifting-left-in-software-engineering](https://dzone.com/articles/shifting-left-in-software-engineering)

[https://www.browserstack.com/guide/what-is-shift-left-testing](https://www.browserstack.com/guide/what-is-shift-left-testing)

[https://www.bmc.com/blogs/what-is-shift-left-shift-left-testing-explained/](https://www.bmc.com/blogs/what-is-shift-left-shift-left-testing-explained/)

[https://katalon.com/resources-center/blog/shift-left-testing-approach](https://katalon.com/resources-center/blog/shift-left-testing-approach)

[https://www.testim.io/blog/shift-left-testing-guide/](https://www.testim.io/blog/shift-left-testing-guide/)

[https://www.dynatrace.com/news/blog/what-is-shift-left-and-what-is-shift-right/#:\~:text=Shift%20left%20is%20the%20practice,performance%20or%20other%20delivery%20processes.](https://www.dynatrace.com/news/blog/what-is-shift-left-and-what-is-shift-right/#:\~:text=Shift%20left%20is%20the%20practice,performance%20or%20other%20delivery%20processes.)

[https://www.ibm.com/topics/continuous-testing](https://www.ibm.com/topics/continuous-testing)

[https://www.ibm.com/topics/shift-left-testing](https://www.ibm.com/topics/shift-left-testing)

[https://insights.sei.cmu.edu/blog/four-types-of-shift-left-testing/](https://insights.sei.cmu.edu/blog/four-types-of-shift-left-testing/)