# EXPERIENCE CONTINUOUS INTEGRATION WITH JENKINS | ANSIBLE | ARTIFACTORY | SONARQUBE | PHP

IMPORTANT NOTICE – This project has some initial theoretical concepts that must be well understood before moving on to the practical
part. Please read it carefully as many times as needed to completely digest the most important and fundamental DevOps concepts.
To successfully implement this project, it is crucial to grasp the importance of the entire CI/CD process, roles of each tool and
success metrics – so we encourage you to thoroughly study the following theory until you feel comfortable explaining all the concepts
(e.g., to your new junior colleague or during a job interview).

In previous projects, you have been deploying a tooling website directly into the
MARKDOWN_HASH40d1b2d83998fabacb726e5bc3d22129MARKDOWNHASH directory on dev servers. Well, even though that worked fine, and we were
able to access the website, it is not the best way to do it. Real world web application code written on
[Java](<https://en.wikipedia.org/wiki/Java(programming_language)>), .NET or other compiled programming languages require a build
stage to create an executable file. The executable file (e.g., jar file in case of Java) contains all the codes embedded, and the
necessary library dependencies, which the application needs to run and work successfully.

Some other programs written languages such as PHP, JavaScript or Python work directly without being built into an executable file –
these languages are called interpreted. That is why we could easily deploy the entire code from git into var/www/html and immediately
the webserver was able to render the pages in a browser. However, it is not optimal to download code directly from Git onto our servers.
There is a smarter way to package the entire application code, and track release versions. We can package the entire code and all its
dependencies into an archive such as .tar.gz or .zip, so that it can be easily unpacked on a respective environment’s servers.

For a better understanding of the difference between compiled vs interpreted programming languages – read this short article.
( https://www.freecodecamp.org/news/compiled-versus-interpreted-languages/ )

In this project, you will understand and get hands on experience around the entire concept around CI/CD from applications perspective.
To fully gain real expertise around this idea, it is best to see it in action across different programming languages and from the
platform perspective too. From the application perspective, we will be focusing on PHP here; there are more projects ahead that are
based on Java, Node.js, .Net and Python. By the time you start working on Terraform, Docker and Kubernetes projects, you will get to
see the platform perspective of CI/CD in action.

What is Continuous Integration?
In software engineering, Continuous Integration (CI) is a practice of merging all developers’ working copies to a shared mainline
(e.g., Git Repository or some other version control system) several times per day. Frequent merges reduce chances of any conflicts
in code and allow to run tests more often to avoid massive rework if something goes wrong. This principle can be formulated as
Commit early, push often.

The general idea behind multiple commits is to avoid what is generally considered as Merge Hell or Integration hell. When a new
developer joins a new project, he or she must create a copy of the main codebase by starting a new feature branch from the mainline
to develop his own features (in some organization or team, this could be called a develop, main or master branch). If there are
tens of developers working on the same project, they will all have their own branches created from mainline at different points in
time. Once they make a copy of the repository it starts drifting away from the mainline with every new merge of other developers’
codes. If this lingers on for a very long time without reconciling the code, then this will cause a lot of code conflict or Merge
Hell, as rightly said. Imagine such a hell from tens of developers or worse, hundreds. So, the best thing to do, is to continuously
commit & push your code to the mainline. As many times as tens times per day. With this practice, you can avoid Merge Hell or
Integration hell.

CI concept is not only about committing your code. There is a general workflow, let us start it…

- Run tests locally: Before developers commit their code to a central repository, it is recommended to test the code locally. So,
  Test-Driven Development (TDD) approach is commonly used in combination with CI. Developers write tests for their code called
  unit-tests, and before they commit their work, they run their tests locally. This practice helps a team to avoid having one
  developer’s work-in-progress code from breaking other developers’ copy of the codebase.

- Compile code in CI: After testing codes locally, developers commit and push their work to a central repository. Rather than building
  the code into an executable locally, a dedicated CI server picks up the code and runs the build there. In this project we will use,
  already familiar to you, Jenkins as our CI server. Build happens either periodically – by polling the repository at some configured
  schedule, or after every commit. Having a CI server where builds run is a good practice for a team, as everyone has visibility into
  each commit and its corresponding builds.

- Run further tests in CI: Even though tests have been run locally by developers, it is important to run the unit-tests on the CI
  server as well. But, rather than focusing solely on unit-tests, there are other kinds of tests and code analysis that can be run
  using CI server. These are extremely critical to determining the overall quality of code being developed, how it interacts with
  other developers’ work, and how vulnerable it is to attacks. A CI server can use different tools for Static Code Analysis, Code
  Coverage Analysis, Code smells Analysis, and Compliance Analysis. In addition, it can run other types of tests such as Integration
  and Penetration tests. Other tasks performed by a CI server include production of code documentation from the source code and
  facilitate manual quality assurance (QA) testing processes.

- Deploy an artifact from CI: At this stage, the difference between CI and CD is spelt out. As you now know, CI is Continuous
  Integration, which is everything we have been discussing so far. CD on the other hand is Continuous Delivery which ensures that
  software checked into the mainline is always ready to be deployed to users. The deployment here is manually triggered after
  certain QA tasks are passed successfully. There is another CD known as Continuous Deployment which is also about deploying the
  software to the users, but rather than manual, it makes the entire process fully automated. Thus, Continuous Deployment is just
  one step ahead in automation than Continuous Delivery.

Continuous Integration in The Real World
To emphasize a typical CI Pipeline further, let us explore the diagram below a little deeper.

![6045](https://user-images.githubusercontent.com/85270361/210156611-11582c6b-8be8-4aae-8661-eb6fb6ce1e9b.PNG)

- Version Control: This is the stage where developers’ code gets committed and pushed after they have tested their work locally.

- Build: Depending on the type of language or technology used, we may need to build the codes into binary executable files
  (in case of compiled languages) or just package the codes together with all necessary dependencies into a deployable package
  (in case of interpreted languages).

- Unit Test: Unit tests that have been developed by the developers are tested. Depending on how the CI job is configured, the entire
  pipeline may fail if part of the tests fails, and developers will have to fix this failure before starting the pipeline again. A Job
  by the way, is a phase in the pipeline. Unit Test is a phase, therefore it can be considered a job on its own.

- Deploy: Once the tests are passed, the next phase is to deploy the compiled or packaged code into an artifact repository. This is
  where all the various versions of code including the latest will be stored. The CI tool will have to pick up the code from this
  location to proceed with the remaining parts of the pipeline.

- Auto Test: Apart from Unit testing, there are many other kinds of tests that are required to analyse the quality of code and
  determine how vulnerable the software will be to external or internal attacks. These tests must be automated, and there can be
  multiple environments created to fulfil different test requirements. For example, a server dedicated for Integration Testing will
  have the code deployed there to conduct integration tests. Once that passes, there can be other sub-layers in the testing phase in
  which the code will be deployed to, so as to conduct further tests. Such are User Acceptance Testing (UAT), and another can be
  Penetration Testing. These servers will be named according to what they have been designed to do in those environments. A UAT server
  is generally be used for UAT, SIT server is for Systems Integration Testing, PEN Server is for Penetration Testing and they can be
  named whatever the naming style or convention in which the team is used. An environment does not necessarily have to reside on one
  single server. In most cases it might be a stack as you have defined in your Ansible Inventory. All the servers in the inventory/dev
  are considered as Dev Environment. The same goes for inventory/stage (Staging Environment) inventory/preprod
  (Pre-production environment), inventory/prod (Production environment), etc. So, it is all down to naming convention as agreed and used
  company or team wide.

- Deploy to production: Once all the tests have been conducted and either the release manager or whoever has the authority to
  authorize the release to the production server is happy, he gives green light to hit the deploy button to ship the release to
  production environment. This is an Ideal Continuous Delivery Pipeline. If the entire pipeline was automated and no human is required
  to manually give the Go decision, then this would be considered as Continuous Deployment. Because the cycle will be repeated, and
  every time there is a code commit and push, it causes the pipeline to trigger, and the loop continues over and over again.

- Measure and Validate: This is where live users are interacting with the application and feedback is being collected for further
  improvements and bug fixes. There are many metrics that must be determined and observed here. We will quickly go through 13 metrics
  that MUST be considered.

Common Best Practices of CI/CD
Before we move on to observability metrics – let us list down the principles that define a reliable and robust CI/CD pipeline:

- Maintain a code repository
- Automate build process
- Make builds self-tested
- Everyone commits to the baseline every day
- Every commit to baseline should be built
- Every bug-fix commit should come with a test case
- Keep the build fast
- Test in a clone of production environment
- Make it easy to get the latest deliverables
- Everyone can see the results of the latest build
- Automate deployment (if you are confident enough in your CI/CD pipeline and willing to go for a fully automated Continuous Deployment)
