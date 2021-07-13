### [测试金字塔实战](https://martinfowler.com/articles/practical-test-pyramid.html)

> [The Practical Test Pyramid](https://martinfowler.com/articles/practical-test-pyramid.html)

“测试金字塔”是一个比喻，它告诉我们要把软件测试按照不同粒度来分组。它也告诉我们每个组应该有多少测试。虽然测试金字塔的概念已经存在了一段时间，但一些团队仍然很难正确将它投入实践。本文重新审视“测试金字塔”最初的概念，并展示如何将其付诸实践。本文将告诉你应该在金字塔的不同层次上寻找何种类型的测试，如何实现这些测试。

> The "Test Pyramid" is a metaphor that tells us to group software tests into buckets of different granularity. It also gives an idea of how many tests we should have in each of these groups. Although the concept of the Test Pyramid has been around for a while, teams still struggle to put it into practice properly. This article revisits the original concept of the Test Pyramid and shows how you can put this into practice. It shows which kinds of tests you should be looking for in the different levels of the pyramid and gives practical examples on how these can be implemented.

2018 年 2 月 26 日

> 26 February 2018

作者：Ham Vocke

> Ham Vocke

Ham 是德国 ThoughtWorks 的一名软件开发和咨询师。由于厌倦了在凌晨 3 点手动部署软件，他开始持续交付实践，加紧自动化步伐，并着手帮助团队高效可靠地交付高质量软件。这样他就可以把省出来的时间用在别的有趣的事情上了。

> Ham is a software developer and consultant at ThoughtWorks in Germany. Being tired of deploying software manually at 3 a.m., he added continuous delivery and diligent automation to his toolbox and set out to help teams deliver high-quality software reliably and efficiently. He makes up for the time gained by annoying people with his antics.

###### 目录

测试自动化的重要性

测试金字塔

我们用到的工具和库

应用例子

* 功能

* 整体架构

* 内部架构

单元测试

* 什么是单元？

* 社交和独处

* 模拟和打桩

* 测试什么？

* 我真的很需要测试私有方法

* 测试架构

* 实现一个单元测试

* 专用的测试工具

集成测试

* 数据库集成

* REST API 集成

* 几个独立服务的集成

* JSON 的解析和撰写

契约测试

* 消费者测试(我们团队)

* 提供者测试(其他团队)

* 提供者测试(我们团队)

UI 测试

端到端测试

* 用户界面端到端测试

* REST API 端到端测试

验收测试 - 你的功能工作正常吗?

探索测试

测试术语误解

把测试放到部署流水线

避免测试重复

整洁测试代码

结论

> ##### Contents

> The Importance of (Test) Automation

> The Test Pyramid

> Tools and Libraries We'll Look at

> The Sample Application

> * Functionality

> * High-level Structure

> * Internal Architecture

> Unit tests

> * What's a Unit?

> * Sociable and Solitary

> * Mocking and Stubbing

> * But I Really Need to Test This Private Method

> * What to Test?

> * Test Structure

> * Specialised Test Helpers

> * Implementing a Unit Test

> Integration Tests

> * Database Integration

> * Integration With Separate Services

> Contract Tests

> * Consumer Test (our team)

> * Provider Test (the other team)

> * Provider Test (our team)

> UI Tests

> End-to-End Tests

> * User Interface End-to-End Test

> * REST API End-to-End Test

> Acceptance Tests — Do Your Features Work Correctly?

> Exploratory Testing

> The Confusion About Testing Terminology

> Putting Tests Into Your Deployment Pipeline

> Avoid Test Duplication

> Writing Clean Test Code

> Conclusion

![](https://martinfowler.com/articles/practical-test-pyramid/teaser.png)

准备上生产环境的软件在上生产之前需要进行测试。随着软件开发行业的成熟，软件测试方法也日趋成熟。开发团队正在逐渐自动化大部分的测试，以此取代大量测试人员手工测试。通过自动化测试，开发团队可以分分钟就知道他们的软件是否被破坏，而不是后知后觉几天后才知道。

> Production-ready software requires testing before it goes into production. As the discipline of software development matured, software testing approaches have matured too. Instead of having myriads of manual software testers development teams have moved towards automating the biggest portion of their testing efforts. Automating your test efforts allows teams to know whether their software is broken in a matter of seconds and minutes instead of days and weeks.

自动化测试极大地缩短了反馈周期，这与敏捷开发实践、持续集成、DevOps 文化等是一脉相承的。拥有高效的软件测试方法，可以让你的团队快速而自信地前行。

> The drastically shortened feedback loop fuelled by automated tests goes hand in hand with agile development practices, continuous delivery and DevOps culture. Having an effective software testing approach allows your team to move fast and confidently.

本文将探讨一个具备高响应力的、可靠并且可维护的测试组合应该如何构建，这与你具体构建的是一个微服务架构、移动应用程序或者物联网生态系统都无关。此外，我们还将详细介绍如何写出高效且可读的自动化测试。

> This article explores what a well-rounded test portfolio should look like to be responsive, reliable and maintainable - regardless of whether you're building a microservices architecture, mobile apps or IoT ecosystems. We'll also get into the details of building effective and readable automated tests.

## （测试）自动化的重要性

软件已经成为我们日常生活中的一个重要组成部分。早期它仅仅用于提高企业的效率，但如今它的作用远不止如此。如今许多公司都想方设法成为一流的数字化公司。作为用户，我们每天使用的软件越来越多。创新的车轮正加速向前滚动。

> Software has become an essential part of the world we live in. It has outgrown its early sole purpose of making businesses more efficient. Today companies try to find ways to become first-class digital companies. As users everyone of us interacts with an ever-increasing amount of software every day. The wheels of innovation are turning faster.

如果你想跟上时代的步伐，你必须研究如何在不牺牲质量的情况下更快地交付你的软件。持续交付——一种高度自动化的、确保你可以随时将软件发布到生产环境中的实践——正能帮你达到这个目的。它通过构建流水线自动测试你的软件，自动其部署到测试和生产环境中。

> If you want to keep pace you'll have to look into ways to deliver your software faster without sacrificing its quality. Continuous delivery, a practice where you automatically ensure that your software can be released into production any time, can help you with that. With continuous delivery you use a build pipeline to automatically test your software and deploy it to your testing and production environments.

软件的数量正以前所未有的速度增长，手动进行构建、测试和部署，很快就会变得不可能——除非你想把所有的时间都用来进行手动重复的工作，而不是用来开发可工作的软件。将一切自动化，从构建到测试，从部署到基础架构，这是你唯一的出路。

> Building, testing and deploying an ever-increasing amount of software manually soon becomes impossible — unless you want to spend all your time with manual, repetitive work instead of delivering working software. Automating everything — from build to tests, deployment and infrastructure — is your only way forward.

![图1](https://martinfowler.com/articles/practical-test-pyramid/buildPipeline.png)

_图 1：使用构建流水线来自动并可靠地将你的软件部署到生产环境_

> Figure 1: Use build pipelines to automatically and reliably get your software into production

传统的软件测试过于依赖手工操作：首先将应用程序部署到测试环境，然后执行一些黑盒测试，例如，通过点击用户界面来查看一切是否工作如常。通常这些测试将由文档指定，以确保测试人员每次测试的内容是一致的。

> Traditionally software testing was overly manual work done by deploying your application to a test environment and then performing some black-box style testing e.g. by clicking through your user interface to see if anything's broken. Often these tests would be specified by test scripts to ensure the testers would do consistent checking.

很明显，手动测试所有更改非常耗时、重复而且繁琐。重复很无趣，无趣就容易犯错，这样子还没测到这周工作结束你就会想找下一份工作了。

> It's obvious that testing all changes manually is time-consuming, repetitive and tedious. Repetitive is boring, boring leads to mistakes and makes you look for a different job by the end of the week.

幸运的是，重复性劳动还是有药可治的：自动化。

> Luckily there's a remedy for repetitive tasks: automation.

自动化繁琐重复的测试将给软件开发人员的生活带来重大改变。自动化这些测试后，你就不需要再一味遵循测试文档点点点以确保软件是否仍正常工作。自动化这些测试，你可以充满自信地修改你的代码。如果你曾试过在没有适当自动化测试的情况下进行大规模重构，那你应该知道这种体验多么恐怖。你怎么知道你是否意外地破坏了某些功能呢？显然，你需要将所有的测试用例手动点一遍。不过老实说，你真的享受这个过程吗？你想象一下，如果你对代码做了大规模改动后惬意地喝了一口咖啡，喝完咖啡后就能马上得知你的改动有没有破坏原有功能。这样的开发体验是不是听起来就让人舒服多了？

> Automating your repetitive tests can be a big game changer in your life as a software developer. Automate these tests and you no longer have to mindlessly follow click protocols in order to check if your software still works correctly. Automate your tests and you can change your codebase without batting an eye. If you've ever tried doing a large-scale refactoring without a proper test suite I bet you know what a terrifying experience this can be. How would you know if you accidentally broke stuff along the way? Well, you click through all your manual test cases, that's how. But let's be honest: do you really enjoy that? How about making even large-scale changes and knowing whether you broke stuff within seconds while taking a nice sip of coffee? Sounds more enjoyable if you ask me.

## 测试金字塔

如果你真的想为你的软件构建自动化测试，你必须知道一个关键的概念：测试金字塔。Mike Cohn 在他的着作《Succeeding with Agile》一书中提出了这个概念。这个比喻非常形象，它让你一眼就知道测试是需要分层的。它还告诉你每一层需要写多少测试。

> If you want to get serious about automated tests for your software there is one key concept you should know about: the test pyramid. Mike Cohn came up with this concept in his book Succeeding with Agile. It's a great visual metaphor telling you to think about different layers of testing. It also tells you how much testing to do on each layer.

![图2](https://martinfowler.com/articles/practical-test-pyramid/testPyramid.png)

_图 2：测试金字塔_

根据 Mike Cohn 的测试金字塔，你的测试组合应该由以下三层组成（自下往上分别是）：

* 单元测试
* 服务测试
* 用户界面测试

> Mike Cohn's original test pyramid consists of three layers that your test suite should consist of (bottom to top):

> * Unit Tests
> * Service Tests
> * User Interface Tests

不幸的是，如果你仔细思考就会发现，测试金字塔的概念有点太短了。有人认为，Mike Cohn 的测试金字塔里的命名或某些概念不是最理想的。我也同意这一点。从当今的角度来看，测试金字塔似乎过于简单了，因此可能会产生误导。

> Unfortunately the concept of the test pyramid falls a little short if you take a closer look. Some argue that either the naming or some conceptual aspects of Mike Cohn's test pyramid are not ideal, and I have to agree. From a modern point of view the test pyramid seems overly simplistic and can therefore be misleading.

然而，由于其简洁性，在建立你自己的测试组合时，测试金字塔本身是一条很好的经验法则。你最好记住 Cohn 测试金字塔中提到的两件事：

* 编写不同粒度的测试
* 层次越高，你写的测试应该越少

> Still, due to it's simplicity the essence of the test pyramid serves as a good rule of thumb when it comes to establishing your own test suite. Your best bet is to remember two things from Cohn's original test pyramid:

> * Write tests with different granularity
> * The more high-level you get the fewer tests you should have

为了维持金字塔形状，一个健康、快速、可维护的测试组合应该是这样的：写许多小而快的单元测试。适当写一些更粗粒度的测试，写很少高层次的端到端测试。注意不要让你的测试变成[冰淇淋](https://www.thoughtworks.com/insights/blog/introducing-software-testing-cupcake-anti-pattern)那样子，这对维护来说将是一个噩梦，并且跑一遍也需要太多时间。

> Stick to the pyramid shape to come up with a healthy, fast and maintainable test suite: Write lots of small and fast unit tests. Write some more coarse-grained tests and very few high-level tests that test your application from end to end. Watch out that you don't end up with a test ice-cream cone that will be a nightmare to maintain and takes way too long to run.

不要太拘泥于 Cohn 测试金字塔中各层次的名字。事实上，它们可能相当具有误导性：服务测试是一个难以掌握的术语（Cohn 本人说他观察到[很多开发人员完全忽略了这一层](https://www.mountaingoatsoftware.com/blog/the-forgotten-layer-of-the-test-automation-pyramid)）。在单页应用框架（如 react，angular，ember.js 等）的时代，UI 测试显然不必位于金字塔的最高层，你完全能够用这些框架对 UI 进行单元测试。

> Don't become too attached to the names of the individual layers in Cohn's test pyramid. In fact they can be quite misleading: service test is a term that is hard to grasp (Cohn himself talks about the observation that a lot of developers completely ignore this layer). In the days of single page application frameworks like react, angular, ember.js and others it becomes apparent that UI tests don't have to be on the highest level of your pyramid - you're perfectly able to unit test your UI in all of these frameworks.

考虑到原始名称的缺点，只要在你的代码库和团队讨论中达成一致，你完全可以为测试层次提供其他名称。

> Given the shortcomings of the original names it's totally okay to come up with other names for your test layers, as long as you keep it consistent within your codebase and your team's discussions.

## 我们将使用的工具和库

* [JUnit](http://junit.org/junit5/): 测试执行库
* [Mockito](http://site.mockito.org/): 模拟依赖
* [Wiremock](http://wiremock.org/): 为外部服务打桩
* [Pact](https://docs.pact.io/): 用于编写消费者驱动的契约测试
* [Selenium](http://docs.seleniumhq.org/): 用于编写用户界面驱动的端到端测试
* [REST-assured](https://github.com/rest-assured/rest-assured): 用于编写 REST API 驱动的端到端测试

> * JUnit: our test runner
> * Mockito: for mocking dependencies
> * Wiremock: for stubbing out external services
> * Pact: for writing CDC tests
> * Selenium: for writing UI-driven end-to-end tests
> * REST-assured: for writing REST API-driven end-to-end tests

## 示例应用

我已经写好了一个[简单的微服务应用](https://github.com/hamvocke/spring-testing)，其中涵盖了测试金字塔各种层次的测试。

> I've written a simple microservice including a test suite with tests for the different layers of the test pyramid.

示例应用体现了一个典型的微服务的特点。它提供了一个 REST 接口，与数据库进行通信并从第三方 REST 服务中获取信息。它是使用 Spring Boot 实现的，即使你之前从未使用过 Spring Boot，它也简单到应该让你很容易理解。

> The sample application shows traits of a typical microservice. It provides a REST interface, talks to a database and fetches information from a third-party REST service. It's implemented in Spring Boot and should be understandable even if you've never worked with Spring Boot before.

请下载 Github 上的代码。Readme 里写了你在计算机上运行应用程序及其自动化测试所需的说明。

> Make sure to check out the code on Github. The readme contains instructions you need to run the application and its automated tests on your machine.

##### 功能

应用的功能十分简单。它提供了三个 REST 接口：

* `GET /hello` 总是返回"Hello World"
* `GET /hello/{lastname}` 根据 lastname 来查询人，如果查到了结果将返回"Hello {Firstname} {Lastname}"
* `GET /weather` 返回现在德国汉堡的天气情况

> The application's functionality is simple. It provides a REST interface with three endpoints:

> * `GET /hello` Returns "Hello World". Always.
>   `GET /hello/{lastname}` Looks up the person with the provided last name. If the person is known, returns "Hello {Firstname} {Lastname}".
>   `GET /weather` Returns the current weather conditions for Hamburg, Germany.

##### 高层架构

从高层次来看，这个系统的结构是这样：

> On a high-level the system has the following structure:

![图3](https://martinfowler.com/articles/practical-test-pyramid/testService.png)

_图 3：我们微服务系统的高层架构_

我们的微服务提供了一个可以通过 HTTP 调用的 REST 接口。对于某些接口，服务将从数据库获取信息。在其他情况下，服务将通过 HTTP 调用外部[天气 API](https://darksky.net/forecast/40.7127,-74.0059/us12/en)来获取并显示当前天气状况。

> Our microservice provides a REST interface that can be called via HTTP. For some endpoints the service will fetch information from a database. In other cases the service will call an external weather API via HTTP to fetch and display current weather conditions.

##### 内部架构

在内部，Spring Service 有一个典型的 Spring 架构：

> Internally, the Spring Service has a Spring-typical architecture:

![图4](https://martinfowler.com/articles/practical-test-pyramid/testArchitecture.png)

_图 4：我们微服务的内部架构_

* `Controller` 提供 REST 接口，处理 HTTP 请求和响应
* `Repository` 和数据库打交道，关注数据在持久化存储里的读写操作
* `Client` 和别的 API 交互，在我们的应用里它会通过 HTTPS 从 darksky.net 获取天气情况
* `Domain` 这是我们的[领域模型](https://en.wikipedia.org/wiki/Domain_model)，它包含了领域逻辑（相对来说，在我们的示例中不甚重要）

> * `Controller` classes provide REST endpoints and deal with HTTP requests and responses
> * `Repository` classes interface with the database and take care of writing and reading data to/from persistent storage
> * `Client` classes talk to other APIs, in our case it fetches JSON via HTTPS from the darksky.net weather API
> * `Domain` classes capture our [domain model](https://en.wikipedia.org/wiki/Domain_model) including the domain logic (which, to be fair, is quite trivial in our case).

有经验的 Spring 开发人员可能会注意到这里缺失了一个常用的层次：受[Domain-Driven Design](https://en.wikipedia.org/wiki/Domain-driven_design)的启发，很多开发人员通常会构建一个由服务类组成的服务层。我决定不在这个应用中包含服务层。原因之一是我们的应用程序很简单，服务层只会成为一个不必要的中间层。另一个是我认为人们过度使用服务层。我经常遇到在服务类中写了全部业务逻辑的代码库。领域模型仅仅成为数据层，而不是行为（[贫血域模型](https://en.wikipedia.org/wiki/Anemic_domain_model)）。对于每一个稍有复杂度的应用来说，这浪费了很多让代码保持结构良好且易于测试的优秀方案，并且没能充分利用面向对象的威力。

> Experienced Spring developers might notice that a frequently used layer is missing here: Inspired by Domain-Driven Design a lot of developers build a service layer consisting of service classes. I decided not to include a service layer in this application. One reason is that our application is simple enough, a service layer would have been an unnecessary level of indirection. The other one is that I think people overdo it with service layers. I often encounter codebases where the entire business logic is captured within service classes. The domain model becomes merely a layer for data, not for behaviour (an Anemic Domain Model). For every non-trivial application this wastes a lot of potential to keep your code well-structured and testable and does not fully utilise the power of object orientation.

我们的 repositories 非常简单，它提供简单的 CRUD 功能。为了保持代码简单，我使用了 Spring Data。 Spring Data 为我们提供了一个简单而通用的 CRUD 实现，我们可以直接使用而不需再造轮子。它还负责为我们的测试启动一个内存数据库，而不是像生产中一样使用真正的 PostgreSQL 数据库。

> Our repositories are straightforward and provide simple CRUD functionality. To keep the code simple I used Spring Data. Spring Data gives us a simple and generic CRUD repository implementation that we can use instead of rolling our own. It also takes care of spinning up an in-memory database for our tests instead of using a real PostgreSQL database as it would in production.

看看代码库，熟悉一下内部结构。这将有助于我们的下一步：测试我们的应用！

> Take a look at the codebase and make yourself familiar with the internal structure. It will be useful for our next step: Testing the application!

## 单元测试

单元测试将成为你测试组合的基石。你的单元测试保证了代码库里的某个单元（被测试的主体）能按照预期那样工作。单元测试在你的测试组合里测试的范围是最窄的。它的数量在测试组合中应该远远多于其他类型的测试。

> The foundation of your test suite will be made up of unit tests. Your unit tests make sure that a certain unit (your subject under test) of your codebase works as intended. Unit tests have the narrowest scope of all the tests in your test suite. The number of unit tests in your test suite will largely outnumber any other type of test.

![图5](https://martinfowler.com/articles/practical-test-pyramid/unitTest.png)

_图 5：一个用测试替身隔绝了外部依赖的典型单元测试_

#### 一个单元指的是什么？

如果你去问三个人同样的问题：“单元”在单元测试的上下文中意味着什么，你很可能会获得四种非常相似的答案。某种程度上讲，对“单元”的定义取决于你自己，因此这个问题没有标准答案。

> If you ask three different people what "unit" means in the context of unit tests, you'll probably receive four different, slightly nuanced answers. To a certain extent it's a matter of your own definition and it's okay to have no canonical answer.

如果你正在使用函数式语言，一个单元最有可能指的是一个函数。你的单元测试将使用不同的参数调用这个函数，并断言它返回了期待的结果。在面向对象语言里，下至一个方法，上至一个类都可以是一个单元（从一个单一的方法到一整个的类都可以是一个单元）。

> If you're working in a functional language a unit will most likely be a single function. Your unit tests will call a function with different parameters and ensure that it returns the expected values. In an object-oriented language a unit can range from a single method to an entire class.

#### 群居和独居

一些人主张，应该将被测试主体下的所有合作者（比如在测试里被你的类调用的其他类）都使用模拟或者桩替换掉，这样可以建立完美的隔离，避免副作用和复杂的测试准备。而有些人主张，只有那些执行起来很慢或者有较大副作用的合作者（比如读写数据库或者发送网络请求的类）才应该被模拟或者打桩替代。

> Some argue that all collaborators (e.g. other classes that are called by your class under test) of your subject under test should be substituted with mocks or stubs to come up with perfect isolation and to avoid side-effects and a complicated test setup. Others argue that only collaborators that are slow or have bigger side effects (e.g. classes that access databases or make network calls) should be stubbed or mocked.

[偶尔](https://martinfowler.com/bliki/UnitTest.html)有人会把用桩隔离所有依赖的测试称为独居单元测试，把和依赖有交互的测试成为群居单元测试（Jay Fields 的[《Working Effectively with Unit Tests》](https://leanpub.com/wewut)这本书里创造了这些概念）。如果有空你可以继续深究下去，读一读不同思想流派各自的[利弊](https://martinfowler.com/articles/mocksArentStubs.html)在哪。

> [Occasionally](https://martinfowler.com/bliki/UnitTest.html) people label these two sorts of tests as solitary unit tests for tests that stub all collaborators and sociable unit tests for tests that allow talking to real collaborators (Jay Fields' [Working Effectively with Unit Tests](https://leanpub.com/wewut) coined these terms). If you have some spare time you can go down the rabbit hole and [read more about the pros and cons](https://martinfowler.com/articles/mocksArentStubs.html) of the different schools of thought.

说到底，决定采用群居方式还是独居方式的单元测试其实并不重要。写自动化测试才是重要的。就我自己而言，我发现我自己经常两种方式都用。如果使用真正的合作者很麻烦，我就会用模拟对象或者桩。如果我觉得引用真正的合作者能让我对测试更有信心，我会仅仅打桩替代掉 service 最外层的依赖。

> At the end of the day it's not important to decide if you go for solitary or sociable unit tests. Writing automated tests is what's important. Personally, I find myself using both approaches all the time. If it becomes awkward to use real collaborators I will use mocks and stubs generously. If I feel like involving the real collaborator gives me more confidence in a test I'll only stub the outermost parts of my service.

#### 模拟和打桩(这里以及下文的桩都指 stub)

模拟对象和桩是两种不一样的测试替身（测试替身还不止这两种）。很多人会混用模拟对象和桩这两个概念。我认为，准确的用词会好点，并且最好能将它们各自的特性谙熟于心。你可以使用测试替身来替换掉真实的对象，给它一个可以更方便测试的实现。

> Mocks and Stubs are two different kinds of Test Doubles (there are more than these two). A lot of people use the terms Mock and Stub interchangeably. I think it's good to be precise and keep their specific properties in mind. You can use test doubles to replace objects you'd use in production with an implementation that helps you with testing.

换句话说，这意味着你是用一个假的实现来代替真的那个（例如，一个类，一个模块或者一个函数）。这个假的实现外表和行为和真的很像（都能响应同样的方法调用），只不过真实的响应内容是你在单元测试开始前就定义好的。

> In plain words it means that you replace a real thing (e.g. a class, module or function) with a fake version of that thing. The fake version looks and acts like the real thing (answers to the same method calls) but answers with canned responses that you define yourself at the beginning of your unit test.

并不是在单元测试时我们才使用测试替身。还有很多精妙的测试替身能以非常可控的方式来模拟整个系统的功能。然而，在单元测试里使用模拟对象和桩的概率会更高（取决于你是喜欢群居风格还是独居风格的开发者），这主要是因为现代语言和库使得构建模拟对象和桩变得更加简单了。

> Using test doubles is not specific to unit testing. More elaborate test doubles can be used to simulate entire parts of your system in a controlled way. However, in unit testing you're most likely to encounter a lot of mocks and stubs (depending of whether you're the sociable or solitary kind of developer), simply because lots of modern languages and libraries make it easy and comfortable to set up mocks and stubs.

不管你的技术选型是怎么样的，一般来说，编程语言的标准库或一些比较有名的三方库都会提供一些优雅的方式来帮你构建 mocks。即使需要自己编写 mock 对象，也只是写一个假类/模块/函数的事，只需要让它与真实的合作者有相同的签名，并设置到你的测试中去即可。

> Regardless of your technology choice, there's a good chance that either your language's standard library or some popular third-party library will provide you with elegant ways to set up mocks. And even writing your own mocks from scratch is only a matter of writing a fake class/module/function with the same signature as the real one and setting up the fake in your test.

你的单元测试跑起来应该非常快。在一般的机器上跑完数千个单元测试应该只需要几分钟。为了得到快速的单元测试，你应该独立地测试代码库的每一小块，并避免进行真实的数据库操作、文件系统操作，或者发送真实的 HTTP 请求（使用模拟对象和桩来隔离这一部分）。

> Your unit tests will run very fast. On a decent machine you can expect to run thousands of unit tests within a few minutes. Test small pieces of your codebase in isolation and avoid hitting databases, the filesystem or firing HTTP queries (by using mocks and stubs for these parts) to keep your tests fast.

一旦你掌握了写单元测试的诀窍，你写起来就能越来越顺畅。打桩隔离掉外部依赖，准备一些输入数据，调用被测试的主体，然后检查返回值是不是你所期待的。看看[测试驱动开发](https://en.wikipedia.org/wiki/Test-driven_development)，让单元测试指引你的开发；如果使用得当，测试驱动开发将帮你进入一个非常顺畅的工作流，它能帮你创造出一个良好且可维护的设计，顺便还能送你一套全面且自动化的测试。当然，测试驱动开发并不是银弹。但是建议你尝试一下，看看它是否适合你。

> Once you got a hang of writing unit tests you will become more and more fluent in writing them. Stub out external collaborators, set up some input data, call your subject under test and check that the returned value is what you expected. Look into [Test-Driven Development](https://en.wikipedia.org/wiki/Test-driven_development) and let your unit tests guide your development; if applied correctly it can help you get into a great flow and come up with a good and maintainable design while automatically producing a comprehensive and fully automated test suite. Still, it's no silver bullet. Go ahead, give it a real chance and see if it feels right for you.

#### 测试什么？

单元测试有个好处，就是你可以为所有的产品代码类写单元测试，而不需要管它们的功能如何，或者它们在内部结构中属于哪个层次。你可以对 controller 进行单元测试，也可以用同样的方式对 repository、领域类或者文件读写类进行单元测试。良好的开端，从坚持**一个实现类就有一个测试类**的法则开始。

> The good thing about unit tests is that you can write them for all your production code classes, regardless of their functionality or which layer in your internal structure they belong to. You can unit tests controllers just like you can unit test repositories, domain classes or file readers. Simply stick to the **one test class per production class** rule of thumb and you're off to a good start.

一个单元测试类至少应该测试**这个类的公共接口**。私有方法不能直接测试的原因是你不能从测试类直接调用它们。受保护的和包私有的方法可以被测试类直接调用（如果测试类和生产代码类的包结构是一样的），但是测试这些方法可能就太过了。

> A unit test class should at least **test the public interface of the class**. Private methods can't be tested anyways since you simply can't call them from a different test class. Protected or package-private are accessible from a test class (given the package structure of your test class is the same as with the production class) but testing these methods could already go too far.

编写单元测试有一条细则：它们应该保证你代码所有的路径都被测试到（包括正常路径和边缘路径）。同时它们不应该和代码的实现有太紧密的耦合。

> There's a fine line when it comes to writing unit tests: They should ensure that all your non-trivial code paths are tested (including happy path and edge cases). At the same time they shouldn't be tied to your implementation too closely.

为什么这样说呢？

> Why's that?

测试如果与产品代码耦合太紧，很快就会令人讨厌。当你重构代码时（快速回顾一下：重构意味着改变代码的内部结构而不改变其对外的行为）你的单元测试就会挂掉。

> Tests that are too close to the production code quickly become annoying. As soon as you refactor your production code (quick recap: refactoring means changing the internal structure of your code without changing the externally visible behaviour) your unit tests will break.

这样的话你就损失了单元测试的一大好处：充当代码变更的保护网。你很快就会厌烦这些愚蠢的测试，而不会感到它能带来好处，因为你每次重构测试就会挂掉，带来更多的工作量。不过说起来这些愚蠢的测试又是谁把它写成这样的呢？

> This way you lose one big benefit of unit tests: acting as a safety net for code changes. You rather become fed up with those stupid tests failing every time you refactor, causing more work than being helpful; and whose idea was this stupid testing stuff anyways?

那么正确的做法是什么？是不要在你的单元测试里耦合实现代码的内部结构。要测试可观测的行为。你应该这样思考：

> What do you do instead? Don't reflect your internal code structure within your unit tests. Test for observable behaviour instead. Think about

_如果我的输入是 x 和 y，输出会是 z 吗？_

> _if I enter values x and y, will the result be z?_

而不是这样：

> instead of

_如果我的输入是 x 和 y，那么这个方法会先调用 A 类，然后调用 B 类，接着输出 A 类和 B 类返回值相加的结果吗？_

> _if I enter x and y, will the method call class A first, then call class B and then return the result of class A plus the result of class B?_

私有方法应该被视为实现细节。这就是为什么你不应该有去测试他们的冲动。

> Private methods should generally be considered an implementation detail. That's why you shouldn't even have the urge to test them.

我经常听单元测试（或者 TDD）的反对者说，编写单元测试是无意义的工作，因为为了获得一个高的测试覆盖率，你必须测试所有的方法。他们经常引用这样的场景：一个过于激昂的团队领导强硬地让他们为 getter、setter 及其他所有琐碎的代码施加测试，以达到 100%的测试覆盖率。

> I often hear opponents of unit testing (or TDD) arguing that writing unit tests becomes pointless work where you have to test all your methods in order to come up with a high test coverage. They often cite scenarios where an overly eager team lead forced them to write unit tests for getters and setters and all other sorts of trivial code in order to come up with 100% test coverage.

这就大错特错啦。

> There's so much wrong with that.

确实你应该测试公共接口。但是更重要的是，**不要去测试微不足道的代码**。别担心，[Kent Beck 说这样是 OK 的](https://stackoverflow.com/questions/153234/how-deep-are-your-unit-tests/)。你不会因为测试 getter，setter 抑或是其他简单的实现（比如没有任何条件逻辑的实现）而得到任何价值。把时间省出来，你就能多参加一个会了，万岁！       

> Yes, you should test the public interface. More importantly, however, you **don't test trivial code**. Don't worry, [Kent Beck said it's ok](https://stackoverflow.com/questions/153234/how-deep-are-your-unit-tests/). You won't gain anything from testing simple getters or setters or other trivial implementations (e.g. without any conditional logic). Save the time, that's one more meeting you can attend, hooray!

##### 我真的很需要测试私有方法

如果你发现自己陷入了真的很需要测试私有方法这个困境中时，先后退一步问问自己，是什么原因导致你想测试私有方法。

> If you ever find yourself in a situation where you really really need to test a private method you should take a step back and ask yourself why.

我很肯定，这背后更可能是一个设计问题，而不仅仅是方法可见性的问题。很可能你觉得需要测试私有方法的原因是方法过于复杂，并且如果通过类的公开接口来测试它，需要做一大堆麻烦的准备工作。

> I'm pretty sure this is more of a design problem than a scoping problem. Most likely you feel the need to test a private method because it's complex and testing this method through the public interface of the class requires a lot of awkward setup.

每当我发现自己身陷类似困境时，我通常会得出这样的结论：我要测试的这个类已经过于复杂了。这个类肯定是做了太多事情，违背了SOLID原则中的单一职责原则。

> Whenever I find myself in this situation I usually come to the conclusion that the class I'm testing is already too complex. It's doing too much and violates the single responsibility principle - the S of the five SOLID principles.

我的解决方案通常是把原来的类拆分成两个类。通常来说，花上一两分钟思考就能找到按照职责拆分的办法，把一个大类拆分为两个小一些的类。我会把我原来急于想测试的私有方法搬移到新的类中，然后让旧类去调用这个新类上的方法。哈，现在这个超级难测试的私有办法变成公有的了，对它添加测试变得易如反掌。除此之外，我还改善了代码结构，使之遵循单一职责原则。

> The solution that often works for me is to split the original class into two classes. It often only takes one or two minutes of thinking to find a good way to cut the one big class into two smaller classes with individual responsibility. I move the private method (that I urgently want to test) to the new class and let the old class call the new method. Voilà, my awkward-to-test private method is now public and can be tested easily. On top of that I have improved the structure of my code by adhering to the single responsibility principle.

#### 测试结构

一个好的测试结构（不局限于单元测试）是这样的：

> A good structure for all your tests (this is not limited to unit tests) is this one:

1.  准备测试数据

> 1.Set up the test data

2.  调用被测方法

> 2.Call your method under test

3.  断言返回的是你期待的结果

> 3.Assert that the expected results are returned

这里有个口诀可以帮你记住这种结构：[“Arrange，Act，Assert”](https://xp123.com/articles/3a-arrange-act-assert/)。另一个口诀则是从 BDD 获取的灵感。就是[“given”，“when”，“then”](https://martinfowler.com/bliki/GivenWhenThen.html)三件套，given 说的是准备数据，when 指的是调用方法，then 则是断言。

> There's a nice mnemonic to remember this structure: ["Arrange, Act, Assert"](https://xp123.com/articles/3a-arrange-act-assert/). Another one that you can use takes inspiration from BDD. It's the ["given", "when", "then"](https://martinfowler.com/bliki/GivenWhenThen.html) triad, where given reflects the setup, when the method call and then the assertion part.

这种模式也可以应用于其他更高层次的测试。在任何情况下，它们都能让你的测试保持一致，易于阅读。除此之外，使用这种结构写出来的测试，往往更简短，更具表达力。

> This pattern can be applied to other, more high-level tests as well. In every case they ensure that your tests remain easy and consistent to read. On top of that tests written with this structure in mind tend to be shorter and more expressive.

#### 实现一个单元测试

知道了测什么、如何组织单元测试后，我们终于可以看一个真正的例子了。

> Now that we know what to test and how to structure our unit tests we can finally see a real example.

让我们来看一个简化版的 ExampleController 类：

> Let's take a simplified version of the ExampleController class:

```java
@RestController
public class ExampleController {

    private final PersonRepository personRepo;

    @Autowired
    public ExampleController(final PersonRepository personRepo) {
        this.personRepo = personRepo;
    }

    @GetMapping("/hello/{lastName}")
    public String hello(@PathVariable final String lastName) {
        Optional<Person> foundPerson = personRepo.findByLastName(lastName);

        return foundPerson
                .map(person -> String.format("Hello %s %s!",
                        person.getFirstName(),
                        person.getLastName()))
                .orElse(String.format("Who is this '%s' you're talking about?",
                        lastName));
    }
}
```

一个针对`hello(lastname)`方法的单元测试可能是这样的：

> A unit test for the hello(lastname) method could look like this:

```java
public class ExampleControllerTest {

    private ExampleController subject;

    @Mock
    private PersonRepository personRepo;

    @Before
    public void setUp() throws Exception {
        initMocks(this);
        subject = new ExampleController(personRepo);
    }

    @Test
    public void shouldReturnFullNameOfAPerson() throws Exception {
        Person peter = new Person("Peter", "Pan");
        given(personRepo.findByLastName("Pan"))
            .willReturn(Optional.of(peter));

        String greeting = subject.hello("Pan");

        assertThat(greeting, is("Hello Peter Pan!"));
    }

    @Test
    public void shouldTellIfPersonIsUnknown() throws Exception {
        given(personRepo.findByLastName(anyString()))
            .willReturn(Optional.empty());

        String greeting = subject.hello("Pan");

        assertThat(greeting, is("Who is this 'Pan' you're talking about?"));
    }
}
```

我们写单元测试用的是[JUnit](http://junit.org/)，Java 实际意义上的标准测试框架。我们使用[Mockito](http://site.mockito.org/)来打桩隔离掉真正的`PersonRepository`类。这个桩允许我们在测试里重新定义 PersonRepository 被调用后产生的响应。桩能让我们的测试更简单，更可预测，更容易组织测试数据。

> We're writing the unit tests using [JUnit](http://junit.org/), the de-facto standard testing framework for Java. We use [Mockito](http://site.mockito.org/) to replace the real PersonRepository class with a stub for our test. This stub allows us to define canned responses the stubbed method should return in this test. Stubbing makes our test more simple, predictable and allows us to easily setup test data.

依照 Arrange，Act，Assert 的结构，我们写了两个单元测试：一个是正常的场景，另一个是找不到搜索人的场景。首先，正常场景创建了一个新的 person 对象，然后告诉 mock 类，当你接受到以“Pan”作为参数的调用时，返回这个 person 对象。这个测试接着调用了被测试方法。最后它断言返回值是等于期待结果的。

> Following the arrange, act, assert structure, we write two unit tests - a positive case and a case where the searched person cannot be found. The first, positive test case creates a new person object and tells the mocked repository to return this object when it's called with "Pan" as the value for the lastName parameter. The test then goes on to call the method that should be tested. Finally it asserts that the response is equal to the expected response.

第二个测试和第一个类似，但它测试的是被测方法找不到对应人名时的场景。

> The second test works similarly but tests the scenario where the tested method does not find a person for the given parameter.

#### 专用的测试工具

不管处在应用架构的哪一层，都能为整个代码库编写单元测试——这是一件美妙的事情。这个例子展示了一个简单的controller单元测试。遗憾的是，用这种方法测试Spring中的controller有个缺陷：Spring MVC中的controller大量依赖于注解来声明其监听的路径、使用的HTTP方法、从URL或query参数中解析的参数等等。在单元测试中直接调用一个controller方法无法测试到这些至关重要的东西。幸运的是，Spring团队提供了一个很棒的测试工具，它可以帮助你写出更好的单元测试。这个工具库叫MockMVC，一定记得去看看。它提供了一套好用的DSL，你可以用它给controller发送假的请求并验证一切是否运转良好。我已经在示例代码库里加入了这样的一个例子。很多框架都提供了专门的测试工具，帮助你更优雅地对代码库的特定层面进行测试。看看你所使用框架的文档，看看它们是否为你编写自动化测试提供了有用的工具。

> It's a thing of beauty that you can write unit tests for your entire codebase, regardless of what layer of your application's architecture you're on. The example shows a simple unit test for a controller. Unfortunately, when it comes to Spring's controllers there's a downside to this approach: Spring MVC's controller make heavy use of annotations to declare which paths they're listening on, which HTTP verbs to use, which parameters they parse from the URL path or query params and so on. Simply invoking a controller's method within your unit tests won't test all of these crucial things. Luckily, the Spring folks came up with a nice test helper you can use to write better controller tests. Make sure to check out MockMVC. It gives you a nice DSL you can use to fire fake requests against your controller and check that everything's cool. I've included an example in the sample codebase. A lot of frameworks offer test helpers to make testing specific aspects of your codebase more pleasant. Check out the documentation of your framework of choice and see if it offers any useful helpers for your automated tests.

## 集成测试

所有常见的应用都会和一些外部环境做集成（数据库，文件系统，向其他应用发起网络请求）。为了使测试有更好的隔离、运行更快，我们通常不会在编写单元测试时涉及这些外部依赖。不过，这些交互始终是存在的，它们也需要被测试覆盖到。这正是集成测试的用处所在。它们测试的是应用与所有外部依赖的集成。

> All non-trivial applications will integrate with some other parts (databases, filesystems, network calls to other applications). When writing unit tests these are usually the parts you leave out in order to come up with better isolation and faster tests. Still, your application will interact with other parts and this needs to be tested. Integration Tests are there to help. They test the integration of your application with all the parts that live outside of your application.

对于自动化测试来说，不仅需要运行自己的应用，也需要运行与之集成的组件。如果要测试和数据库的集成，那就需要在跑测试的时候运行数据库。如果要测试能否从硬盘里读取文件，就需要先保存一个文件到硬盘上，然后在集成测试中去读取它。

> For your automated tests this means you don't just need to run your own application but also the component you're integrating with. If you're testing the integration with a database you need to run a database when running your tests. For testing that you can read files from a disk you need to save a file to your disk and load it in your integration test.

前面我提到过「单元测试」是一个模糊的术语，对于集成测试而言，更是如此。对于一些人来讲，集成测试意味着去测试和多方应用产生交互的整个应用。我理解的集成测试更加狭义：每次只测试一个集成点。测试时应使用测试替身来替代其他的外部服务、数据库等。同时，使用契约测试对测试替身和真实实现进行覆盖。这样出来的集成测试更快，更独立，更易理解和调试。

> I mentioned before that "unit tests" is a vague term, this is even more true for "integration tests". For some people integration testing means to test through the entire stack of your application connected to other applications within your system. I like to treat integration testing more narrowly and test one integration point at a time by replacing separate services and databases with test doubles. Together with contract testing and running contract tests against test doubles as well as the real implementations you can come up with integration tests that are faster, more independent and usually easier to reason about.

狭义的集成测试测的是服务的边界。从概念上来说，这样的测试总是在触发导致应用和外部依赖（文件系统，数据库，其他服务等）集成的行为。比如说，一个数据库集成测试可能会这么写：

> Narrow integration tests live at the boundary of your service. Conceptually they're always about triggering an action that leads to integrating with the outside part (filesystem, database, separate service). A database integration test would look like this:

![图6](https://martinfowler.com/articles/practical-test-pyramid/dbIntegrationTest.png)

_图 6：一个集成了你的代码和数据库的集成测试_

1.  启动数据库
2.  连接应用到数据库
3.  调用被测函数，该函数会往数据库写数据
4.  读取数据库，查看期望的数据是不是被写到了数据库里

> 1.  start a database
> 2.  connect your application to the database
> 3.  trigger a function within your code that writes data to the database
> 4.  check that the expected data has been written to the database by reading the data from the database

另一个例子，一个通过 REST API 和外部服务集成的测试可能是会这么写:

> Another example, testing that your service integrates with a separate service via a REST API could look like this:

![图7](https://martinfowler.com/articles/practical-test-pyramid/httpIntegrationTest.png)

_图 7：这种集成测试检查了应用是否能正确和外部服务通信_

1.  启动应用
2.  启动一个被测外部服务的实例（或者一个具有相同接口的测试替身）
3.  调用被测函数，该函数会从外部服务的 API 读取数据
4.  检查应用是否能正确解析返回结果

> 1.  start your application
> 2.  start an instance of the separate service (or a test double with the same interface)
> 3.  trigger a function within your code that reads from the separate service's API
> 4.  check that your application can parse the response correctly

与单元测试一样，集成测试也可以写得很白盒。一些框架在应用启动后，仍然支持对应用的一些部分进行 mock。 这使得你可以去检查正确的交互是否发生。

> Your integration tests - like unit tests - can be fairly whitebox. Some frameworks allow you to start your application while still being able to mock some other parts of your application so that you can check that the correct interactions have happened.

代码中所有涉及数据序列化和反序列化的地方都要写集成测试。这些场景可能比你想象得更多，比如说：

> Write integration tests for all pieces of code where you either serialize or deserialize data. This happens more often than you might think. Think about:

* 调用自身服务的 REST API
* 读写数据库
* 调用外部服务的 API
* 读写队列
* 写入文件系统

> * Calls to your services' REST API
> * Reading from and writing to databases
> * Calling other application's APIs
> * Reading from and writing to queues
> * Writing to the filesystem

为这些边界编写集成测试，保证了对外部系统的数据读写操作是正常工作的。

> Writing integration tests around these boundaries ensures that writing data to and reading data from these external collaborators works fine.

编写狭义的集成测试时，你应该尽可能在本地运行外部依赖，如启动一个本地的 MySQL 数据库、针对本地的 ext4 文件系统进行测试等。如果是与外部服务集成，可以在本地运行该服务的实例，或构建一个模拟真实服务的假服务，并在本地运行。

> When writing narrow integration tests you should aim to run your external dependencies locally: spin up a local MySQL database, test against a local ext4 filesystem. If you're integrating with a separate service either run an instance of that service locally or build and run a fake version that mimics the behaviour of the real service.

如果有些三方服务，你没法在本地运行一个实例，那么可以考虑运行一个专用实例，并在集成测试中指向它。避免在自动化测试里集成真实的生产环境的服务。在生产环境上爆出上千个测试请求是个惹人生气的好办法，因为你会扰乱日志（这是最好的情况），最坏的情况是你会对该服务产生 DoS 攻击。透过网络和一个服务集成是广义集成测试的一大特征，这会让你的测试更慢，通常也更难编写。

> If there's no way to run a third-party service locally you should opt for running a dedicated test instance and point at this test instance when running your integration tests. Avoid integrating with the real production system in your automated tests. Blasting thousands of test requests against a production system is a surefire way to get people angry because you're cluttering their logs (in the best case) or even DoS 'ing their service (in the worst case). Integrating with a service over the network is a typical characteristic of a broad integration test and makes your tests slower and usually harder to write.

在测试金字塔中，集成测试的层级比单元测试更高。集成缓慢的外部依赖（如文件系统或数据库等）通常比隔离了这些依赖的单元测试需要更长时间。他们可能比小型并且独立的单元测试难写，毕竟你需要让外部依赖在你的测试中运行起来。然而，它的优势在于建立了你对应用能正确访问外部依赖的自信，这是单纯的单元测试做不到的。

> With regards to the test pyramid, integration tests are on a higher level than your unit tests. Integrating slow parts like filesystems and databases tends to be much slower than running unit tests with these parts stubbed out. They can also be harder to write than small and isolated unit tests, after all you have to take care of spinning up an external part as part of your tests. Still, they have the advantage of giving you the confidence that your application can correctly work with all the external parts it needs to talk to. Unit tests can't help you with that.

#### 数据库集成

`PersonRepository` 是代码里唯一的数据库类。它依赖于*Spring Data*，我们并没有实际去实现它。只需要继承`CrudRepository`接口并声明一个方法名。剩下的就是 Spring 魔法了，Spring 会帮我们实现其他所有的东西。

> The `PersonRepository` is the only repository class in the codebase. It relies on _Spring Data_ and has no actual implementation. It just extends the `CrudRepository` interface and provides a single method header. The rest is Spring magic.

```
public interface PersonRepository extends CrudRepository<Person, String> {
    Optional<Person> findByLastName(String lastName);
}
```

对于`CrudRepository`接口，Spring Boot 提供了完整的 CRUD 方法例如`findOne`, `findAll`, `save`, `update`和`delete`。我们自定义的方法（`findByLastName()`）继承了这些基础功能并实现了根据 last name 获取 Persons 对象的功能。Spring Data 会解析方法的返回类型，并按照命名规范解析方法名，从而决定如何实现方法。

> With the `CrudRepository` interface Spring Boot offers a fully functional CRUD repository with `findOne`, `findAll`, `save`, `update` and `delete` methods. Our custom method definition (`findByLastName()`) extends this basic functionality and gives us a way to fetch Persons by their last name. Spring Data analyses the return type of the method and its method name and checks the method name against a naming convention to figure out what it should do.

虽然 Spring Data 已经实现了和数据库交互的功能，我还是写了一个数据库集成测试。你可能会反对，认为这是在测试框架，而我们应该避免测试不属于我们开发的代码。然则，我坚信在这里写一个集成测试是至关重要的。首先它测试了我们自定义的 findByLastName 方法实际的行为如我们所愿。次之，它证明了我们的数据库类正确地使用了 Spring 的装配特性，它是能正确连接到数据库的。

> Although Spring Data does the heavy lifting of implementing database repositories I still wrote a database integration test. You might argue that this is testing the framework and something that I should avoid as it's not our code that we're testing. Still, I believe having at least one integration test here is crucial. First it tests that our custom findByLastName method actually behaves as expected. Secondly it proves that our repository used Spring's wiring correctly and can connect to the database.

为了让你能更容易在本地把测试运行起来（而不必真的装一个 PostgreSQL 数据库），我们的测试会连接到一个内存*H2*数据库。

> To make it easier for you to run the tests on your machine (without having to install a PostgreSQL database) our test connects to an in-memory _H2_ database.

我已经在`build.gradle`里定义 H2 作为测试依赖。而测试目录下的`application.properties`没有定义任何`spring.datasource`属性。这会告诉 Spring Data 使用内存数据库，它会在 classpath 里找到 H2 来跑我们的测试。

> I've defined H2 as a test dependency in the `build.gradle` file. The `application.properties` in the test directory doesn't define any `spring.datasource` properties. This tells Spring Data to use an in-memory database. As it finds H2 on the classpath it simply uses H2 when running our tests.

当你用 int profile 真正启动应用时（例如把`SPRING_PROFILES_ACTIVE=int`设置到环境变量里），它会连接到`application-int.properties`里定义的 PostgreSQL 数据库。

> When running the real application with the int profile (e.g. by setting `SPRING_PROFILES_ACTIVE=int` as environment variable) it connects to a PostgreSQL database as defined in the `application-int.properties`.

我知道这涉及到了很多 Spring 的知识。你必须仔细阅读[许多文档](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-sql.html#boot-features-embedded-database-support)才能理解这个例子。实现代码只有几行，非常直观，但是如果你不知道 Spring 的一些知识点是很难加以理解的。

> I know, that's an awful lot of Spring specifics to know and understand. To get there, you'll have to sift through [a lot of documentation](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-sql.html#boot-features-embedded-database-support). The resulting code is easy on the eye but hard to understand if you don't know the fine details of Spring.

除此以外，测试使用内存数据库其实是有风险的。毕竟我们集成测试针对的数据库和我们生产用的数据库不一样。你可以自己选择，是利用 Spring 的强大能力获得简洁的代码，亦或者是写显式但较为冗长的实现。

> On top of that going with an in-memory database is risky business. After all, our integration tests run against a different type of database than they would in production. Go ahead and decide for yourself if you prefer Spring magic and simple code over an explicit yet more verbose implementation.

解释已经足够多了，这里有一个集成测试的例子。它先保存了一个 Person 对象到数据库里，然后根据 last name 去查找它。

> Enough explanation already, here's a simple integration test that saves a Person to the database and finds it by its last name:

```java
@RunWith(SpringRunner.class)
@DataJpaTest
public class PersonRepositoryIntegrationTest {
    @Autowired
    private PersonRepository subject;

    @After
    public void tearDown() throws Exception {
        subject.deleteAll();
    }

    @Test
    public void shouldSaveAndFetchPerson() throws Exception {
        Person peter = new Person("Peter", "Pan");
        subject.save(peter);

        Optional<Person> maybePeter = subject.findByLastName("Pan");

        assertThat(maybePeter, is(Optional.of(peter)));
    }
}
```

你可以看到我们的集成测试像单元测试那样遵循了*arrange*, _act_, *assert*的结构。我说过这是一个普适的概念吧。

> You can see that our integration test follows the same _arrange_, _act_, _assert_ structure as the unit tests. Told you that this was a universal concept!

#### 和外部服务集成

我们的微服务会调用[darksky.net](https://darksky.net/)——一个关于天气的 REST API。当然啦，我们希望保证服务调用时能发送正确的请求，并且能正确地解析响应。

> Our microservice talks to [darksky.net](https://darksky.net/), a weather REST API. Of course we want to ensure that our service sends requests and parses the responses correctly.

跑自动化测试时，我们希望避免真实地调用*darksky*的服务。当然，我们使用的免费版有调用次数限制，这是个原因。但真正的原因是要*去耦合*。我们的测试应该能独立运行，而不需要管 darsky.net 可爱的开发者们在干些啥。即使我们的机器访问不到*darksky*服务器，或*darksky*服务器在进行宕机维护，都不应该使我们的测试挂掉。

> We want to avoid hitting the real _darksky_ servers when running automated tests. Quota limits of our free plan are only part of the reason. The real reason is _decoupling_. Our tests should run independently of whatever the lovely people at darksky.net are doing. Even when your machine can't access the _darksky_ servers or the darksky servers are down for maintenance.

我们可以在集成测试中用自己的假*darksky*服务器来代替真正的服务器。这听起来像是个巨大的任务。幸亏有像[Wiremock](http://wiremock.org/)这样的工具，事情变得很简单。看这里：

> We can avoid hitting the real _darksky_ servers by running our own, fake _darksky_ server while running our integration tests. This might sound like a huge task. Thanks to tools like [Wiremock](http://wiremock.org/) it's easy peasy. Watch this:

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class WeatherClientIntegrationTest {

    @Autowired
    private WeatherClient subject;

    @Rule
    public WireMockRule wireMockRule = new WireMockRule(8089);

    @Test
    public void shouldCallWeatherService() throws Exception {
        wireMockRule.stubFor(get(urlPathEqualTo("/some-test-api-key/53.5511,9.9937"))
                .willReturn(aResponse()
                        .withBody(FileLoader.read("classpath:weatherApiResponse.json"))
                        .withHeader(CONTENT_TYPE, MediaType.APPLICATION_JSON_VALUE)
                        .withStatus(200)));

        Optional<WeatherResponse> weatherResponse = subject.fetchWeather();

        Optional<WeatherResponse> expectedResponse = Optional.of(new WeatherResponse("Rain"));
        assertThat(weatherResponse, is(expectedResponse));
    }
}
```

为了使用 Wiremock，我们在固定的端口(8089)实例化了一个`WireMockRule`。使用领域特定语言，我们可以配置一个 Wiremock 服务器，定义它需要监听的路径并设置相应的应答。

> To use Wiremock we instantiate a `WireMockRule` on a fixed port (8089). Using the DSL we can set up the Wiremock server, define the endpoints it should listen on and set canned responses it should respond with.

接着调用我们要测试的方法——它会调用第三方服务，然后检查结果是否能被正确解析。

> Next we call the method we want to test, the one that calls the third-party service and check if the result is parsed correctly.

理解测试怎样调用 Wiremock 服务器而不是真正的*darksky*很重要。秘密就在`src/test/resources`下的`application.properties`文件。这是 Spring 在运行测试时会加载的属性文件。出于测试目的——比如，调用一个假的 Wiremock 服务器而不是真实服务器——我们在这个文件里覆写了一些配置，如 API keys 和 URL 等：

> It's important to understand how the test knows that it should call the fake Wiremock server instead of the real _darksky_ API. The secret is in our `application.properties` file contained in `src/test/resources`. This is the properties file Spring loads when running tests. In this file we override configuration like API keys and URLs with values that are suitable for our testing purposes, e.g. calling the fake Wiremock server instead of the real one:

`weather.url = http://localhost:8089`

值得注意的一点是，这里声明的端口必须和我们在测试里实例化`WireMockRule`时的端口保持一致。我们之所以能为了测试注入一个假的 API url，是因为我们通过注入的方式将 url 传给了`WeatherClient`类的构造函数：

> Note that the port defined here has to be the same we define when instantiating the `WireMockRule` in our test. Replacing the real weather API's URL with a fake one in our tests is made possible by injecting the URL in our `WeatherClient` class' constructor:

```java
@Autowired
public WeatherClient(final RestTemplate restTemplate,
                     @Value("${weather.url}") final String weatherServiceUrl,
                     @Value("${weather.api_key}") final String weatherServiceApiKey) {
    this.restTemplate = restTemplate;
    this.weatherServiceUrl = weatherServiceUrl;
    this.weatherServiceApiKey = weatherServiceApiKey;
}
```

这样我们告诉`WeatherClient`要把我们定义在 application properties 的`weather.url`值赋给`weatherUrl`。

> This way we tell our `WeatherClient` to read the `weatherUrl` parameter's value from the `weather.url` property we define in our application properties.

借助类似 Wiremock 这样的工具，为外部服务编写*狭义的集成测试*就变得很简单。不幸的是这种方式有个缺点：如何保证我们启动的假服务器与真的服务行为一致？按我们目前的实现，当外部服务改变了它的 API 时，我们的测试依然能跑过。现在我们仅仅测试了`WeatherClient`可以解析假服务器返回的应答信息。这是个好的开始，但是它非常脆弱。如果使用*端到端测试*，针对真实服务的实例运行测试，而不使用假的服务，固然能解决这个问题，但这又会让我们对被测服务的可用性产生依赖。幸运的是，针对这个难题还是有更好的方案：针对真实和假的服务运行契约测试。这能保证我们集成测试里用的假服务是个忠实的测试替身。下面来看看这种方案是怎么工作的。

> Writing _narrow integration tests_ for a separate service is quite easy with tools like Wiremock. Unfortunately there's a downside to this approach: How can we ensure that the fake server we set up behaves like the real server? With the current implementation, the separate service could change its API and our tests would still pass. Right now we're merely testing that our `WeatherClient` can parse the responses that the fake server sends. That's a start but it's very brittle. Using _end-to-end tests_ and running the tests against a test instance of the real service instead of using a fake service would solve this problem but would make us reliant on the availability of the test service. Fortunately, there's a better solution to this dilemma: Running contract tests against the fake and the real server ensures that the fake we use in our integration tests is a faithful test double. Let's see how this works next.

## 契约测试

越来越多现代软件组织发现，对于增长的开发需求，可以让不同的团队来开发同一系统的不同部分。每个团队负责构建独立、松耦合的服务，团队间开发不互相影响。最终再将这些服务集成为一个大而全的系统。最近关于微服务的讨论日益热烈，关注的正是这一点。

> More modern software development organisations have found ways of scaling their development efforts by spreading the development of a system across different teams. Individual teams build individual, loosely coupled services without stepping on each others toes and integrate these services into a big, cohesive system. The more recent buzz around microservices focuses on exactly that.

将系统拆分成多个更小的服务，常常意味着这些服务之间需要通过确定的（最好是定义明确的，但有时候会有变动演进）接口通信。

> Splitting your system into many small services often means that these services need to communicate with each other via certain (hopefully well-defined, sometimes accidentally grown) interfaces.

不同应用间的接口可能形态各异，或基于不同的技术栈。常见的有：

> Interfaces between different applications can come in different shapes and technologies. Common ones are

* 基于 HTTPS 使用 JSON 交互的 REST 接口
* 基于类似[gRPC](https://grpc.io/)的 RPC（Remote Procedure Call，远程进程调用）接口
* 使用队列构建的事件驱动架构

> * REST and JSON via HTTPS
> * RPC using something like [gRPC](https://grpc.io/)
> * building an event-driven architecture using queues

对于任意一个接口，一定会涉及两个实体：提供方和消费方。**提供方**为消费方提供数据。**消费方**处理来自提供方的数据。在 REST 世界里，提供方为所有要暴露的 API 创建一个 REST API；消费方则调用这些 API 来获取数据，或进一步触发其他的服务。而在一个由异步、事件驱动的世界，提供方（通常被称为**发布者**）发布数据到一个队列中；消费方（通常被称为**订阅者**）订阅这些队列，读取并处理相关数据。

> For each interface there are two parties involved: the provider and the consumer. The **provider** serves data to consumers. The **consumer** processes data obtained from a provider. In a REST world a provider builds a REST API with all required endpoints; a consumer makes calls to this REST API to fetch data or trigger changes in the other service. In an asynchronous, event-driven world, a provider (often rather called **publisher**) publishes data to a queue; a consumer (often called **subscriber**) subscribes to these queues and reads and processes data.

![图8](https://martinfowler.com/articles/practical-test-pyramid/contract_tests.png)

_图 8：每一个接口都有提供方（或者发布者）和消费方（或者订阅者）实体。接口之间的规范可以视为是一个契约。_

_Figure 8: Each interface has a providing (or publishing) and a consuming (or subscribing) party. The specification of an interface can be considered a contract._

当你把服务消费方和服务提供方分散到不同的团队去时，你就需要清楚地了解这些服务之间的接口（也就是我们所讲的**契约**）。传统的公司一般是通过以下的方式解决这个问题：

> As you often spread the consuming and providing services across different teams you find yourself in the situation where you have to clearly specify the interface between these services (the so called **contract**). Traditionally companies have approached this problem in the following way:

* 写一个钜细靡遗的接口文档（就是契约）
* 根据定义好的契约实现提供方服务
* 把接口文档扔给隔壁的消费团队
* 等。等到消费方团队实现接口消费部分的工作
* 运行一些大型的、手动的系统测试，保证软件能正常工作
* 祈祷双方团队永远都维持接口定义不变，不要把事情搞砸

> * Write a long and detailed interface specification (the contract)
> * Implement the providing service according to the defined contract
> * Throw the interface specification over the fence to the consuming team
> * Wait until they implement their part of consuming the interface
> * Run some large-scale manual system test to see if everything works
> * Hope that both teams stick to the interface definition forever and don't screw up

越来越多现代软件开发团队已经把第五步和第六步用更加自动化的方式来替代：自动化[契约测试](https://martinfowler.com/bliki/ContractTest.html)保证了消费方和提供方实现的时候依然遵循契约。这种测试提供了一个良好的回归测试组合，保证契约的变更能被及早发现。

> More modern software development teams have replaced steps 5. and 6. with something more automated: Automated [contract tests](https://martinfowler.com/bliki/ContractTest.html) make sure that the implementations on the consumer and provider side still stick to the defined contract. They serve as a good regression test suite and make sure that deviations from the contract will be noticed early.

在现代敏捷组织，你应该选择效率高浪费少的路子。你们是在同一个公司里构建应用。比起扔出去一个面面俱到的文档，与其他服务的开发者们直接交流本应容易得多。毕竟他们是你的同事，而不是一个只能通过客户支持或合同进行沟通的第三方供应商。

> In a more agile organisation you should take the more efficient and less wasteful route. You build your applications within the same organisation. It really shouldn't be too hard to talk to the developers of the other services directly instead of throwing overly detailed documentation over the fence. After all they're your co-workers and not a third-party vendor that you could only talk to via customer support or legally bulletproof contracts.

\*_消费方驱动的契约测试_（Consumer-Driven Contract tests，CDC 测试）是让[消费方驱动契约实现](https://martinfowler.com/articles/consumerDrivenContracts.html)。使用 CDC，接口消费方会写测试，检查接口是不是返回了他们想要的所有数据。消费方团队会发布这些测试从而让提供方可以轻松获取到这些测试并执行。提供方团队现在可以一边运行 CDC 测试一边开发他们的 API 了。一旦所有测试通过，他们就知道已经实现了所有消费方想要的东西。

> **Consumer-Driven Contract tests** (CDC tests) let the [consumers drive the implementation of a contract](https://martinfowler.com/articles/consumerDrivenContracts.html). Using CDC, consumers of an interface write tests that check the interface for all data they need from that interface. The consuming team then publishes these tests so that the publishing team can fetch and execute these tests easily. The providing team can now develop their API by running the CDC tests. Once all tests pass they know they have implemented everything the consuming team needs.

![图9](https://martinfowler.com/articles/practical-test-pyramid/cdc_tests.png)

_图 9：契约测试保证了提供方和所有的消费方基于同一个定义好的接口契约。用 CDC 测试，消费者就可以通过自动化测试发布他们的需求，提供方则可以持续不断获取这些测试并执行_
_Figure 9: Contract tests ensure that the provider and all consumers of an interface stick to the defined interface contract. With CDC tests consumers of an interface publish their requirements in the form of automated tests; the providers fetch and execute these tests continuously_

这种方式允许提供方团队只实现必要的东西（让设计保持简约，[YAGNI](https://martinfowler.com/bliki/Yagni.html)等）。提供方团队需要持续地获取并运行这些 CDC 测试（从他们的构建 Pipeline 里），从而能立即发现任何打破契约的代码变更。如果有代码变更破坏了接口，CDC 测试应该会执行失败，这样可以防止破坏性改动上线。当这些测试保持通过，团队就可以做任何他们想做的改动而不需要担心其他团队。使用消费方驱动测试的话，一般过程会是这样的：

> This approach allows the providing team to implement only what's really necessary (keeping things simple, YAGNI and all that). The team providing the interface should fetch and run these CDC tests continuously (in their build pipeline) to spot any breaking changes immediately. If they break the interface their CDC tests will fail, preventing breaking changes to go live. As long as the tests stay green the team can make any changes they like without having to worry about other teams. The Consumer-Driven Contract approach would leave you with a process looking like this:

* 消费方团队根据他们期待的结果编写自动化测试
* 发布自动化测试给提供方团队
* 提供方持续不断地运行这些测试，并保持他们都能通过
* 如果 CDC 测试挂掉了，则需要双方进行沟通

> * The consuming team writes automated tests with all consumer expectations
> * They publish the tests for the providing team
> * The providing team runs the CDC tests continuously and keeps them green
> * Both teams talk to each other once the CDC tests break

如果你的组织正在践行微服务，那么拥有 CDC 测试将是迈向自治团队的一大步。CDC 测试是一种促进团队交流的自动化途径。它们保证了团队间的接口能一直如期工作。如果有 CDC 测试挂掉，则可能是个好的信号，意味着你应该走过去到那个被测试影响的团队，了解他们最近是否有 API 变更，弄清楚你们希望如何处理这些变更。

> If your organisation adopts a microservices approach, having CDC tests is a big step towards establishing autonomous teams. CDC tests are an automated way to foster team communication. They ensure that interfaces between teams are working at any time. Failing CDC tests are a good indicator that you should walk over to the affected team, have a chat about any upcoming API changes and figure out how you want to move forward.

一个稚嫩的 CDC 测试实现非常简单，比如说你可以对一个 API 发送请求，并断言响应中包含了你需要的所有东西。然后你把这些测试打包成可执行文件（.gem, .jar, .sh），并将它们上传到一个其他团队可以获取到的地方（例如一些诸如[Artifactory](https://www.jfrog.com/artifactory/)这样的仓库）。

> A naive implementation of CDC tests can be as simple as firing requests against an API and assert that the responses contain everything you need. You then package these tests as an executable (.gem, .jar, .sh) and upload it somewhere the other team can fetch it (e.g. an artifact repository like [Artifactory](https://www.jfrog.com/artifactory/)).

在过去几年里，CDC 正变得越来越受欢迎。同时也涌现了一些工具，使得编写及上传 CDC 更加简单。

> Over the last couple of years the CDC approach has become more and more popular and several tools been build to make writing and exchanging them easier.

在这些工具中，[Pact](https://github.com/realestate-com-au/pact)可能是最显眼的一个了。它为编写提供方或消费方的测试提供了详尽的支持，为外部服务隔离提供了开箱即用的（打）桩工具，它还支持你与其他团队交换 CDC 测试。Pact 已经被移植到很多平台上，并且可以和 JVM 语言一起使用，例如 Ruby，.NET，JavaScript 等等。

> [Pact](https://github.com/realestate-com-au/pact) is probably the most prominent one these days. It has a sophisticated approach of writing tests for the consumer and the provider side, gives you stubs for separate services out of the box and allows you to exchange CDC tests with other teams. Pact has been ported to a lot of platforms and can be used with JVM languages, Ruby, .NET, JavaScript and many more.

如果你想开始编写 CDC 测试但不知道怎么开始，不妨试试 Pact。[文档](https://docs.pact.io/)一开始可能会让你应接不暇。保持耐心克服一下。它能帮助你深刻理解 CDC 测试，也会让你更容易在团队合作时推行 CDC。

> If you want to get started with CDCs and don't know how, Pact can be a sane choice. The [documentation](https://docs.pact.io/) can be overwhelming at first. Be patient and work through it. It helps to get a firm understanding for CDCs which in turn makes it easier for you to advocate for the use of CDCs when working with other teams.

消费方驱动契约测试真的可以说是建立自治团队的基石，它让这样的团队充满自信，快速前行。不要掉队，好好读读相关的文档，尝试一下。一套稳固的 CDC 测试集非常宝贵，它让你能快速开发，同时又不会挂掉已有的服务，引起其他团队的不满。

> Consumer-Driven Contract tests can be a real game changer to establish autonomous teams that can move fast and with confidence. Do yourself a favor, read up on that concept and give it a try. A solid suite of CDC tests is invaluable for being able to move fast without breaking other services and cause a lot of frustration with other teams.

#### 消费方测试（我方团队）

上面的例子中，我们的微服务消费了天气 API。所以我们有责任写一个**消费方测试**来定义我们期望从 API 契约中获得的结果。

> Our microservice consumes the weather API. So it's our responsibility to write a **consumer test** that defines our expectations for the contract (the API) between our microservice and the weather service.

首先，我们要在`build.gradle`里引入一个库来写基于协议的消费方测试：

> First we include a library for writing pact consumer tests in our `build.gradle`:

`testCompile('au.com.dius:pact-jvm-consumer-junit_2.11:3.5.5')`

得益于这个库，我们可以用协议的仿造服务来实现一个消费方测试：

> Thanks to this library we can implement a consumer test and use pact's mock services:

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class WeatherClientConsumerTest {

    @Autowired
    private WeatherClient weatherClient;

    @Rule
    public PactProviderRuleMk2 weatherProvider =
            new PactProviderRuleMk2("weather_provider", "localhost", 8089, this);

    @Pact(consumer="test_consumer")
    public RequestResponsePact createPact(PactDslWithProvider builder) throws IOException {
        return builder
                .given("weather forecast data")
                .uponReceiving("a request for a weather request for Hamburg")
                    .path("/some-test-api-key/53.5511,9.9937")
                    .method("GET")
                .willRespondWith()
                    .status(200)
                    .body(FileLoader.read("classpath:weatherApiResponse.json"),
                            ContentType.APPLICATION_JSON)
                .toPact();
    }

    @Test
    @PactVerification("weather_provider")
    public void shouldFetchWeatherInformation() throws Exception {
        Optional<WeatherResponse> weatherResponse = weatherClient.fetchWeather();
        assertThat(weatherResponse.isPresent(), is(true));
        assertThat(weatherResponse.get().getSummary(), is("Rain"));
    }
}
```

如果观察得仔细，你会发现`WeatherClientConsumerTest`和`WeatherClientIntegrationTest`很相似。这次我们用 Pact 取代了 Wiremock 来对服务器打桩。事实上消费方测试工作方式与集成测试完全一致：我们用打桩的方式隔离第三方服务，定义我们期望的响应，然后检查我们的客户端可以正确处理响应。从这个意义上讲，`WeatherClientConsumerTest`本身就是一个狭义的集成测试。这种方式相比使用 Wiremock 好在，它每次运行都会创建一个*协议文件*（会生成到`target/pacts/&pact-name>.json`）。这个协议文件使用特殊的 JSON 格式描述了这个契约的期望结果，它可以被用来验证我们打桩的服务与真实服务行为确实是一致的。我们可以把这个协议文件交给提供 API 的团队，他们可以根据这个文件的期望输出来编写提供方测试。这样的话他们就能测试，他们的 API 是不是满足我们期望的所有结果。

> If you look closely, you'll see that the `WeatherClientConsumerTest` is very similar to the `WeatherClientIntegrationTest`. Instead of using Wiremock for the server stub we use Pact this time. In fact the consumer test works exactly as the integration test, we replace the real third-party server with a stub, define the expected response and check that our client can parse the response correctly. In this sense the `WeatherClientConsumerTest` is a narrow integration test itself. The advantage over the wiremock-based test is that this test generates a _pact file_ (found in `target/pacts/&pact-name>.json`) each time it runs. This pact file describes our expectations for the contract in a special JSON format. This pact file can then be used to verify that our stub server behaves like the real server. We can take the pact file and hand it to the team providing the interface. They take this pact file and write a provider test using the expectations defined in there. This way they test if their API fulfils all our expectations.

消费方通过描述他们的期望结果来驱动接口实现，这就是 CDC 里消费方驱动所想要表达的意思。提供方必须保证他们满足了所有期望结果。没有过度设计，保持简洁。

> You see that this is where the consumer-driven part of CDC comes from. The consumer drives the implementation of the interface by describing their expectations. The provider has to make sure that they fulfil all expectations and they're done. No gold-plating, no YAGNI and stuff.

把 Pact 文件交给提供方团队可以有几种方式。一种简单方式就是把它们加入到版本控制系统里，告诉提供方永远拉取最新的文件即可。更先进一点的方式则是用一个文件仓库，类似 Amazon S3 这样的服务或者 pact broker。起步迅速，按需拓展。

> Getting the pact file to the providing team can happen in multiple ways. A simple one is to check them into version control and tell the provider team to always fetch the latest version of the pact file. A more advances one is to use an artifact repository, a service like Amazon's S3 or the pact broker. Start simple and grow as you need.

在真实的软件中，你并不需要为一个客户端类既写集成测试又写消费方测试。上面的示例代码同时包含了这两种测试，只是想告诉你这两种测试的写法。如果你想用协议来写 CDC 测试，我推荐你只写消费方测试。两种测试的编写成本是一样的。用协议的方式就有协议文件这个好处，这样把协议文件递交给其他团队，他们就能很容易实现他们的提供方测试。当然这取决于你能说服其他团队也使用协议。如果不行，那么用 Wiremock 来实现集成测试可以作为替代方案。

> In your real-world application you don't need both, an integration test and a consumer test for a client class. The sample codebase contains both to show you how to use either one. If you want to write CDC tests using pact I recommend sticking to the latter. The effort of writing the tests is the same. Using pact has the benefit that you automatically get a pact file with the expectations to the contract that other teams can use to easily implement their provider tests. Of course this only makes sense if you can convince the other team to use pact as well. If this doesn't work, using the integration test and Wiremock combination is a decent plan b.

#### 提供方测试（其他团队）

提供方测试必须由提供天气 API 的团队来实现。我们消费的是 darksky.net 提供的一个公共 API。理论上 darksky 团队会实现提供方测试，以保证不打破他们应用和我们的服务之间的契约。

> The provider test has to be implemented by the people providing the weather API. We're consuming a public API provided by darksky.net. In theory the darksky team would implement the provider test on their end to check that they're not breaking the contract between their application and our service.

很明显他们不会关注我们这个简单的示例代码库，也不会为我们实现 CDC 测试。这是公共 API 和组织内微服务的一大不同点。公共 API 不可能考虑到每一个消费方，否则他们就得整天忙于写测试了。而在我们自己组织内，你能够、也应该考虑每个消费方。你的应用一般只会服务于少量的，最多几十个消费方。为这些接口编写提供方测试应该不是太大的问题，这可以保证系统稳定。

> Obviously they don't care about our meager sample application and won't implement a CDC test for us. That's the big difference between a public-facing API and an organisation adopting microservices. Public-facing APIs can't consider every single consumer out there or they'd become unable to move forward. Within your own organisation, you can — and should. Your app will most likely serve a handful, maybe a couple dozen of consumers max. You'll be fine writing provider tests for these interfaces in order to keep a stable system.

提供方团队拿到协议文件后，会在他们的服务上运行一下。这需要实现一个提供方测试，在测试中读取协议文件，打桩隔离掉一些测试数据，运行他们的服务，并检查是否返回了协议文件中期望的结果。

> The providing team gets the pact file and runs it against their providing service. To do so they implement a provider test that reads the pact file, stubs out some test data and runs the expectations defined in the pact file against their service.

Pact 团队写了一些库来实现提供方测试。他们在主[GitHub 仓库](https://github.com/DiUS/pact-jvm)写了一个很好的概览，告诉你有哪些消费方/提供方测试的库是可用的，你只需要从中选择适用于你技术栈的即可。

> The pact folks have written several libraries for implementing provider tests. Their main [GitHub repo](https://github.com/DiUS/pact-jvm) gives you a nice overview which consumer and which provider libraries are available. Pick the one that best matches your tech stack.

为了简单起见，我们假设 darksky 的 API 也是用 Spring Boot 来实现的。这样的话他们就可以用[Spring pact provider](https://github.com/DiUS/pact-jvm/tree/master/pact-jvm-provider-spring)来写，这个库和 Spring 的 MockMVC 机制做了很好的适配。我们假想 darksky.net 团队写了提供方测试，那么它大概长这样：

> For simplicity let's assume that the darksky API is implemented in Spring Boot as well. In this case they could use the [Spring pact provider](https://github.com/DiUS/pact-jvm/tree/master/pact-jvm-provider-spring) which hooks nicely into Spring's MockMVC mechanisms. A hypothetical provider test that the darksky.net team would implement could look like this:

```java
@RunWith(RestPactRunner.class)
@Provider("weather_provider") // same as the "provider_name" in our clientConsumerTest
@PactFolder("target/pacts") // tells pact where to load the pact files from
public class WeatherProviderTest {
    @InjectMocks
    private ForecastController forecastController = new ForecastController();

    @Mock
    private ForecastService forecastService;

    @TestTarget
    public final MockMvcTarget target = new MockMvcTarget();

    @Before
    public void before() {
        initMocks(this);
        target.setControllers(forecastController);
    }

    @State("weather forecast data") // same as the "given()" in our clientConsumerTest
    public void weatherForecastData() {
        when(forecastService.fetchForecastFor(any(String.class), any(String.class)))
                .thenReturn(weatherForecast("Rain"));
    }
}
```

你可以看到提供方测试必须做的就是两点：加载一个协议文件（例如，用`@PactFolder`注解来自动加载已下载好的协议文件）、提供需要准备的数据（例如使用 Mockito 来仿造）。此外，不需要再实现额外的测试，它会从协议文件自动派生出来。对于消费方测试里声明的*提供方名称（provider name）*和状态，提供方测试应该一一匹配。

> You see that all the provider test has to do is to load a pact file (e.g. by using the `@PactFolder` annotation to load previously downloaded pact files) and then define how test data for pre-defined states should be provided (e.g. using Mockito mocks). There's no custom test to be implemented. These are all derived from the pact file. It's important that the provider test has matching counterparts to the _provider name_ and state declared in the consumer test.

#### 提供方测试（我方团队）

我们已经看了如何测试我们服务和天气提供方之间的契约。对于这个接口，我们的服务扮演的是消费方，天气服务则扮演了提供方。考虑得更远一些，会发现我们的服务同时也是其他系统的提供方：我们为数个路径提供了 REST API 以供其他系统消费。

> We've seen how to test the contract between our service and the weather provider. With this interface our service acts as consumer, the weather service acts as provider. Thinking a little further we'll see that our service also acts as a provider for others: We provide a REST API that offers a couple of endpoints ready to be consumed by others.

我们刚认识到了契约测试什么场景都能用，当然我们也会想给我们的契约写一写契约测试。幸运的是，我们使用了消费方驱动契约，所以我们手里有所有的消费方发过来的协议，可以用它们来实现我们的 REST API 提供方测试。

> As we've just learned that contract tests are all the rage, we of course write a contract test for this contract as well. Luckily we're using consumer-driven contracts so there's all the consuming teams sending us their Pacts that we can use to implement our provider tests for our REST API.

先把 pact-jvm-provider 库装上：

> Let's first add the Pact provider library for Spring to our project:

`testCompile('au.com.dius:pact-jvm-provider-spring_2.12:3.5.5')`

提供方测试的实现与前面所述的范式相同。为简单起见，我直接从[simple consumer](https://github.com/hamvocke/spring-testing-consumer)拿来一份协议文件放到了我们的仓库中，这会让我们操作起来简单一些。在真实的项目，你可能需要更完善的机制来分发协议文件。

> Implementing the provider test follows the same pattern as described before. For the sake of simplicity I simply checked the pact file from our [simple consumer](https://github.com/hamvocke/spring-testing-consumer) into our service's repository. This makes it easier for our purpose, in a real-life scenario you're probably going to use a more sophisticated mechanism to distribute your pact files.

```java
@RunWith(RestPactRunner.class)
@Provider("person_provider")// same as in the "provider_name" part in our pact file
@PactFolder("target/pacts") // tells pact where to load the pact files from
public class ExampleProviderTest {

    @Mock
    private PersonRepository personRepository;

    @Mock
    private WeatherClient weatherClient;

    private ExampleController exampleController;

    @TestTarget
    public final MockMvcTarget target = new MockMvcTarget();

    @Before
    public void before() {
        initMocks(this);
        exampleController = new ExampleController(personRepository, weatherClient);
        target.setControllers(exampleController);
    }

    @State("person data") // same as the "given()" part in our consumer test
    public void personData() {
        Person peterPan = new Person("Peter", "Pan");
        when(personRepository.findByLastName("Pan")).thenReturn(Optional.of
                (peterPan));
    }
}
```

`ExampleProviderTest`需要做的事只有一件，那就是根据协议文件里的内容提供`State`信息。当我们运行提供方测试时，Pact 就会用到指定的协议文件，并发送 HTTP 请求到我们的服务，然后根据我们配置的 State 来决定响应。

> The shown `ExampleProviderTest` needs to provide state according to the pact file we're given, that's it. Once we run the provider test, Pact will pick up the pact file and fire HTTP request against our service that then responds according to the state we've set up.

## UI 测试

大部分的应用都会有些用户界面。在 web 应用的上下文中，我们所谈的界面就是指网页界面。但人们经常会忘记，除了多彩的网页页面，还有许多的 REST API 界面或命令行界面等。

> Most applications have some sort of user interface. Typically we're talking about a web interface in the context of web applications. People often forget that a REST API or a command line interface is as much of a user interface as a fancy web user interface.

*UI 测试*测的是应用中的用户界面是否如预期工作。比如，用户的输入需要触发正确的动作，数据需要能展示给用户看，UI 的状态需要发生正确变化等。

> _UI tests_ test that the user interface of your application works correctly. User input should trigger the right actions, data should be presented to the user, the UI state should change as expected.

![图10](https://martinfowler.com/articles/practical-test-pyramid/ui_tests.png)

有时候，人们提到 UI 测试和端到端测试时（比如 Mike Cohn）说的是一个东西。对我而言，这种观点混淆了这两个有交集的不同概念。

> UI Tests and end-to-end tests are sometimes (as in Mike Cohn's case) said to be the same thing. For me this conflates two things that are rather orthogonal concepts.

诚然，端到端的测试通常意味着会测到许多用户界面。但是反过来讲却并不能成立。

> Yes, testing your application end-to-end often means driving your tests through the user interface. The inverse, however, is not true.

测试用户界面不必非得通过端到端的方式完成。根据技术栈不同，有时测试用户界面也可以很简单，只需要为前端的 JavaScript 代码写一些单元测试，同时用桩将后端隔离开即可。

> Testing your user interface doesn't have to be done in an end-to-end fashion. Depending on the technology you use, testing your user interface can be as simple as writing some unit tests for your frontend javascript code with your backend stubbed out.

对于传统的网页应用，UI 测试可以用[Selenium](http://docs.seleniumhq.org/)这一类工具完成。如果你把 REST API 也当成一个用户界面，对你的 API 写一些恰当的集成测试可以达到完全相同的目的。

> With traditional web applications testing the user interface can be achieved with tools like [Selenium](http://docs.seleniumhq.org/). If you consider a REST API to be your user interface you should have everything you need by writing proper integration tests around your API.

对于网页界面而言，你的 UI 大概可以围绕这几个部分进行测试：行为，布局，可用性，以及少数人认为需要测试的设计一致性。

> With web interfaces there's multiple aspects that you probably want to test around your UI: behaviour, layout, usability or adherence to your corporate design are only a few.

幸运的是，测试用户界面的行为非常简单。点击一下，输入数据，然后看到用户界面状态如实变更。现代的单页应用框架（以[react](https://facebook.github.io/react/), [vue.js](https://vuejs.org/), [Angular](https://angular.io/)等为代表）通常都会提供一些工具或组件，帮你从很低的测试层级（单元测试）对界面交互进行测试。即便你没有使用任何框架，只使用纯 JavaScript，也有常规的测试工具（如[Jasmine](https://jasmine.github.io/)或[Mocha](http://mochajs.org/)等）可供选择。对于更传统一些的服务端渲染应用，使用 Selenium 会是最佳的选择。

> Fortunately, testing the behaviour of your user interface is pretty simple. You click here, enter data there and want the state of the user interface to change accordingly. Modern single page application frameworks ([react](https://facebook.github.io/react/), [vue.js](https://vuejs.org/), [Angular](https://angular.io/) and the like) often come with their own tools and helpers that allow you to thoroughly test these interactions in a pretty low-level (unit test) fashion. Even if you roll your own frontend implementation using vanilla javascript you can use your regular testing tools like [Jasmine](https://jasmine.github.io/) or [Mocha](http://mochajs.org/). With a more traditional, server-side rendered application, Selenium-based tests will be your best choice.

测试应用的布局是否前后一致则有些困难。根据应用类型和用户需求的不同，也许你可能需要确保代码的更改不会意外破坏页面的布局。

> Testing that your web application's layout remains intact is a little harder. Depending on your application and your users' needs you may want to make sure that code changes don't break the website's layout by accident.

问题是众所周知…计算机在检查某物「看起来是否不错」方面一直表现不佳（也许未来一些好的机器学习算法可以改善这一现状）。

> The problem is that computers are notoriously bad at checking if something "looks good" (maybe some clever machine learning algorithm can change that in the future).

如果你依然希望在构建流水线中集成自动化的测试来检查应用的设计，还是有些工具可以试一下。大部分的这些工具都是使用 Selenium 帮你在不同浏览器中打开应用、截图、跟之前的截图做对比。如果新旧截图的差异超过了预设阈值，工具就会告诉你。

> There are some tools to try if you want to automatically check your web application's design in your build pipeline. Most of these tools utilise Selenium to open your web application in different browsers and formats, take screenshots and compare these to previously taken screenshots. If the old and new screenshots differ in an unexpected way, the tool will let you know.

[Galen](http://galenframework.com/)就是其中一种工具。即便你有特殊的需求，自己实现一套工具也不是很难。我之前工作过的一些团队就构建了[lineup](https://github.com/otto-de/lineup)，以及基于 Java 的[jlineup](https://github.com/otto-de/jlineup)，用以实现类似的测试工具。如我前面所说，这两种工具都使用了 Selenium。

> [Galen](http://galenframework.com/) is one of these tools. But even rolling your own solution isn't too hard if you have special requirements. Some teams I've worked with built [lineup](https://github.com/otto-de/lineup) and its Java-based cousin [jlineup](https://github.com/otto-de/jlineup) to achieve something similar. Both tools take the same Selenium-based approach I described before.

当你想测试可用性或一些「看起来对不对」的东西时，你已经超越了自动化测试的范畴。这是[探索性测试](https://en.wikipedia.org/wiki/Exploratory_testing)，可用性测试（这甚至可以像[走廊测试](https://en.wikipedia.org/wiki/Usability_testing#Hallway_testing)那般简单）的领域。你需要给你的用户展示产品，看看他们是否喜欢使用它，有没有什么功能会让他们在使用时感到困惑或恼火。

> Once you want to test for usability and a "looks good" factor you leave the realms of automated testing. This is the area where you should rely on [exploratory testing](https://en.wikipedia.org/wiki/Exploratory_testing), usability testing (this can even be as simple as [hallway testing](https://en.wikipedia.org/wiki/Usability_testing#Hallway_testing)) and showcases with your users to see if they like using your product and can use all features without getting frustrated or annoyed.

## 端到端测试

通过用户界面测试一个已部署好的应用，可以说是最端到端的方式了。前面说的以 webdriver 驱动的 UI 测试就是一个很好的端到端测试案例。

> Testing your deployed application via its user interface is the most end-to-end way you could test your application. The previously described, webdriver driven UI tests are a good example of end-to-end tests.

![图11](https://martinfowler.com/articles/practical-test-pyramid/e2etests.png)

_图 11：端到端测试测试了整个的、完全集成了的系统_

_Figure 11: End-to-end tests test your entire, completely integrated system_

端到端测试（也被称为[广域栈测试](https://martinfowler.com/bliki/BroadStackTest.html)）会赋予你极大的信心，让你了解软件是否正常工作。[Selenium](http://docs.seleniumhq.org/)和[WebDriver 协议](https://www.w3.org/TR/webdriver/)使你能够针对部署好的服务进行自动化测试，它能启动一个无头（headless）浏览器来对用户界面执行点击、输入、检查状态的操作。当然你也可以直接使用 Selenium，或者用类似[Nightwatch](http://nightwatchjs.org/)这种基于 Selenium 的工具。

> End-to-end tests (also called [Broad Stack Tests](https://martinfowler.com/bliki/BroadStackTest.html)) give you the biggest confidence when you need to decide if your software is working or not. [Selenium](http://docs.seleniumhq.org/) and the [WebDriver Protocol](https://www.w3.org/TR/webdriver/) allow you to automate your tests by automatically driving a (headless) browser against your deployed services, performing clicks, entering data and checking the state of your user interface. You can use Selenium directly or use tools that are build on top of it, [Nightwatch](http://nightwatchjs.org/) being one of them.

端到端测试也有它特有的一些问题。众所周知，它们通常比较脆弱，经常因为一些意料之外的问题挂掉。并且这些错误信息通常不是真正的原因所在。用户界面越复杂，测试常常越是脆弱。浏览器差异、时间（时序）问题、元素渲染、意外的弹出框…这还仅是列表一角，已经让我经常花大量时间进行调试，这实在令人沮丧。

> End-to-End tests come with their own kind of problems. They are notoriously flaky and often fail for unexpected and unforeseeable reasons. Quite often their failure is a false positive. The more sophisticated your user interface, the more flaky the tests tend to become. Browser quirks, timing issues, animations and unexpected popup dialogs are only some of the reasons that got me spending more of my time with debugging than I'd like to admit.

在微服务的世界中，谁负责写这些测试也是一个大问题。因为端到端测试覆盖到数个服务（整个系统），导致编写端到端测试不是任何一个团队的责任。

> In a microservices world there's also the big question of who's in charge of writing these tests. Since they span multiple services (your entire system) there's no single team responsible for writing end-to-end tests.

如果你们有一个集中的质量保障团队，由他们来编写端到端测试看起来就不错。但是呢，拥有一个集中式的 QA 团队同时也是一种明显的反模式，这根本不应该出现在 DevOps 的世界中。你的团队应该是真正的跨职能团队。回答谁该对端到端测试负责这个问题并不容易。也许你的组织里会有一些社区实践，或有个*质量协会*之类的机构能为此负责。一个合适的答案，与你的组织本身高度相关。

> If you have a centralised quality assurance team they look like a good fit. Then again having a centralised QA team is a big anti-pattern and shouldn't have a place in a DevOps world where your teams are meant to be truly cross-functional. There's no easy answer who should own end-to-end tests. Maybe your organisation has a community of practice or a _quality guild_ that can take care of these. Finding the correct answer highly depends on your organisation.

此外，端到端测试还需要大量的维护成本，运行起来也相当慢。试想一下这样的场景，除非只有几个微服务，否则你根本没办法在本地运行端到端测试，因为你需要启动所有的服务。祝你的机器能同时跑起几百个应用，并且内存没被撑爆。

> Furthermore, end-to-end tests require a lot of maintenance and run pretty slowly. Thinking about a landscape with more than a couple of microservices in place you won't even be able to run your end-to-end tests locally — as this would require to start all your microservices locally as well. Good luck spinning up hundreds of applications on your development machine without frying your RAM.

因其高昂的维护成本，你应该尽量将端到端测试的数量减少到最低限度。

> Due to their high maintenance cost you should aim to reduce the number of end-to-end tests to a bare minimum.

考虑一下应用中对用户而言具有高价值的交互。定义好产品产生核心价值的用户旅程，然后将这些旅程中最重要的步骤变成自动化的端到端测试。

> Think about the high-value interactions users will have with your application. Try to come up with user journeys that define the core value of your product and translate the most important steps of these user journeys into automated end-to-end tests.

举例来说，假设你正在构建一个电子商务网站，最具价值的顾客旅程可能是这样的：用户搜索一件商品、将其加入购物车，然后付款。就这么简单。只要这个旅程正常工作，你应该无需过多担心。也许你可以找出一两个重要的用户旅程，并将其用端到端测试来覆盖。但到此为止，再多的测试就会开始带来痛苦了。

> If you're building an e-commerce site your most valuable customer journey could be a user searching for a product, putting it in the shopping basket and doing a checkout. That's it. As long as this journey still works you shouldn't be in too much trouble. Maybe you'll find one or two more crucial user journeys that you can translate into end-to-end tests. Everything more than that will likely be more painful than helpful.

谨记：在测试金字塔里，有很多更低层级的测试，这些测试已经全面测试了各种边缘情况及与其他系统的集成。不需要再在高层级测试里重复测一遍。否则，高维护成本和一堆谎报错误将会降低开发速度，迟早会让你对测试失去信心。

> Remember: you have lots of lower levels in your test pyramid where you already tested all sorts of edge cases and integrations with other parts of the system. There's no need to repeat these tests on a higher level. High maintenance effort and lots of false positives will slow you down and cause you to lose trust in your tests, sooner rather than later.

#### 用户界面端到端测试

对于端到端测试来说，[Selenium](http://docs.seleniumhq.org/)和[WebDriver](https://www.w3.org/TR/webdriver/)协议是大部分开发者的选择。用 Selenium 你可以选一个喜欢的浏览器，它会自动帮你访问网页、触发点击事件、输入一些数据，并检查用户界面的变更。

> For end-to-end tests [Selenium](http://docs.seleniumhq.org/) and the [WebDriver](https://www.w3.org/TR/webdriver/) protocol are the tool of choice for many developers. With Selenium you can pick a browser you like and let it automatically call your website, click here and there, enter data and check that stuff changes in the user interface.

Selenium 需要一个浏览器用来运行测试。有几种所谓的*驱动（drivers）* 可以用来启动不同的浏览器。[选一种](https://www.mvnrepository.com/search?q=selenium+driver)（或几种）加到`build.gradle`里。不管选择什么浏览器，都需要保证团队里所有开发者以及 CI 服务器上都安装对应版本的浏览器。保持同步有时相当困难。如果你用的是 Java，有一个轻量级的库叫[webdrivermanager](https://github.com/bonigarcia/webdrivermanager)，它会自动帮你下载并设置好正确版本的浏览器。把这些依赖加到`build.gradle`文件里即可：

> Selenium needs a browser that it can start and use for running its tests. There are multiple so-called _'drivers'_ for different browsers that you could use. [Pick one](https://www.mvnrepository.com/search?q=selenium+driver) (or multiple) and add it to your `build.gradle`. Whatever browser you choose, you need to make sure that all devs in your team and your CI server have installed the correct version of the browser locally. This can be pretty painful to keep in sync. For Java, there's a nice little library called [webdrivermanager](https://github.com/bonigarcia/webdrivermanager) that can automate downloading and setting up the correct version of the browser you want to use. Add these two dependencies to your `build.gradle` and you're good to go:

```
testCompile('org.seleniumhq.selenium:selenium-chrome-driver:2.53.1')
testCompile('io.github.bonigarcia:webdrivermanager:1.7.2')
```

为测试运行一个完整的浏览器有时候会是一种麻烦事。尤其对于持续交付跑 Pipeline 的服务器，也许没有时间打开一个包含用户界面的浏览器(例如因为当前没有可用的 X-Server)。变通办法就是使用类似[xvfb](https://en.wikipedia.org/wiki/Xvfb)那样的虚拟 X-Server。(看来得好好了解一下 X-Server 是啥，否则不翻译可能会有问题）

> Running a fully-fledged browser in your test suite can be a hassle. Especially when using continuous delivery the server running your pipeline might not be able to spin up a browser including a user interface (e.g. because there's no X-Server available). You can take a workaround for this problem by starting a virtual X-Server like [xvfb](https://en.wikipedia.org/wiki/Xvfb).

更先进一点的方案是使用*无头*浏览器（也就是没有用户界面的浏览器）来运行 webdriver 测试。截至目前[PhantomJS](http://phantomjs.org/)依然是做浏览器自动化最好的无头浏览器（译者注：其实现在 PhantomJS 作者已经宣布停止维护了，建议转向使用[Puppeteer](https://github.com/GoogleChrome/puppeteer)）。但这之后[Chromium](https://developers.google.com/web/updates/2017/04/headless-chrome)和[Firefox](https://developer.mozilla.org/en-US/Firefox/Headless_mode)双双宣布他们在各自的浏览器里实现了无头模式，这使得 PhantomJS 突然显得有些过时了。比起仅为了你自己作为开发者的方便而使用人工浏览器，使用用户真正使用的浏览器（如 Firefox 和 Chrome）来做测试应该是更好的选择。

> A more recent approach is to use a _headless_ browser (i.e. a browser that doesn't have a user interface) to run your webdriver tests. Until recently [PhantomJS](http://phantomjs.org/) was the leading headless browser used for browser automation. Ever since both [Chromium](https://developers.google.com/web/updates/2017/04/headless-chrome) and [Firefox](https://developer.mozilla.org/en-US/Firefox/Headless_mode) announced that they've implemented a headless mode in their browsers PhantomJS all of a sudden became obsolete. After all it's better to test your website with a browser that your users actually use (like Firefox and Chrome) instead of using an artificial browser just because it's convenient for you as a developer.

不管是无头 Firefox 还是 Chrome，都是新出的东西，还没有广泛应用到实现 webdriver 测试的场景。这里我们希望简化一些，不去折腾走在时代前沿的浏览器无头模式，而是直接用传统的 Selenium 和一个普通的浏览器。以下是一个简单的端到端测试的例子，它将启动 Chrome、访问我们的服务，并检查页面的内容是否正确：

> Both, headless Firefox and Chrome, are brand new and yet to be widely adopted for implementing webdriver tests. We want to keep things simple. Instead of fiddling around to use the bleeding edge headless modes let's stick to the classic way using Selenium and a regular browser. A simple end-to-end test that fires up Chrome, navigates to our service and checks the content of the website looks like this:

```java
@RunWith(SpringRunner.class)
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
public class HelloE2ESeleniumTest {

    private WebDriver driver;

    @LocalServerPort
    private int port;

    @BeforeClass
    public static void setUpClass() throws Exception {
        ChromeDriverManager.getInstance().setup();
    }

    @Before
    public void setUp() throws Exception {
        driver = new ChromeDriver();
    }

    @After
    public void tearDown() {
        driver.close();
    }

    @Test
    public void helloPageHasTextHelloWorld() {
        driver.get(String.format("http://127.0.0.1:%s/hello", port));

        assertThat(driver.findElement(By.tagName("body")).getText(), containsString("Hello World!"));
    }
}
```

这个测试只能在你装好 Chrome 的系统里运行起来（本地机器，CI 服务器）。

> Note that this test will only run on your system if you have Chrome installed on the system you run this test on (your local machine, your CI server).

这个测试很直观。它用`@SpringBootTest`在一个随机端口启动了整个 Spring 应用。然后我们实例化了一个 Chrome 的 webdriver，告诉它去访问我们微服务的/hello 路径，然后检查浏览器里是不是打印出了"Hello World!"的字样。看起来很酷哟！

> The test is straightforward. It spins up the entire Spring application on a random port using `@SpringBootTest`. We then instantiate a new Chrome webdriver, tell it to go navigate to the /hello endpoint of our microservice and check that it prints "Hello World!" on the browser window. Cool stuff!

#### REST API 端到端测试

在测试应用时，如果能避免涉及图形化的用户界面，将有望写出比完整的端到端测试更健壮的测试，同时依然能覆盖到大部分的应用。这在测试 web 界面异常困难时大有用处。也许你的应用根本没有界面，仅仅是提供了 REST API（比方说你有个单页应用会调用到这个 API，或单纯因为你鄙视一切好用而漂亮的界面）。不管怎么说，有一类[皮下测试](https://martinfowler.com/bliki/SubcutaneousTest.html)仅仅测试图形化用户界面背后的东西，但依然能给你带来足够的信心。如果你也像咱的示例代码一样，只是暴露出一个 REST API，那么这种测试方法就非常合适。

> Avoiding a graphical user interface when testing your application can be a good idea to come up with tests that are less flaky than full end-to-end tests while still covering a broad part of your application's stack. This can come in handy when testing through the web interface of your application is particularly hard. Maybe you don't even have a web UI but serve a REST API instead (because you have a single page application somewhere talking to that API, or simply because you despise everything that's nice and shiny). Either way, a [Subcutaneous Test](https://martinfowler.com/bliki/SubcutaneousTest.html) that tests just beneath the graphical user interface and can get you really far without compromising on confidence too much. Just the right thing if you're serving a REST API like we do in our example code:

```java
@RestController
public class ExampleController {
    private final PersonRepository personRepository;

    // shortened for clarity

    @GetMapping("/hello/{lastName}")
    public String hello(@PathVariable final String lastName) {
        Optional<Person> foundPerson = personRepository.findByLastName(lastName);

        return foundPerson
             .map(person -> String.format("Hello %s %s!",
                     person.getFirstName(),
                     person.getLastName()))
             .orElse(String.format("Who is this '%s' you're talking about?",
                     lastName));
    }
}
```

有一个库，在测试提供 REST API 的服务时很好用：[REST-assured](https://github.com/rest-assured/rest-assured) 。它提供了优雅的 DSL，让你可以优雅地向待测 API 发出真实的 HTTP 请求，并检验收到的响应。

> Let me show you one more library that comes in handy when testing a service that provides a REST API. [REST-assured](https://github.com/rest-assured/rest-assured) is a library that gives you a nice DSL for firing real HTTP requests against an API and evaluating the responses you receive.

第一件事：把这个库加到`build.gradle`里。

> First things first: Add the dependency to your `build.gradle`.

`testCompile('io.rest-assured:rest-assured:3.0.3')`

用这个库，我们可以这样实现我们针对 REST API 的端到端测试：

> With this library at our hands we can implement an end-to-end test for our REST API:

```java
@RunWith(SpringRunner.class)
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
public class HelloE2ERestTest {

    @Autowired
    private PersonRepository personRepository;

    @LocalServerPort
    private int port;

    @After
    public void tearDown() throws Exception {
        personRepository.deleteAll();
    }

    @Test
    public void shouldReturnGreeting() throws Exception {
        Person peter = new Person("Peter", "Pan");
        personRepository.save(peter);

        when()
                .get(String.format("http://localhost:%s/hello/Pan", port))
        .then()
                .statusCode(is(200))
                .body(containsString("Hello Peter Pan!"));
    }
}
```

这里我们还是用`@SpringBootTest`启动了一个完整的 Spring 应用。我们`@Autowire`了一个`PersonRepository`，以便能很容易将测试数据写到数据库里。现在当我们发送了 API 请求，对 Pan 先生打招呼说 hello 时，我们将会收到一个友好的打招呼响应（Hello Peter Pan!）。神奇吧！如果你的应用没有用户界面，那么这个端到端测试就已绰绰有余了。

> Again, we start the entire Spring application using `@SpringBootTest`. In this case we `@Autowire` the `PersonRepository` so that we can write test data into our database easily. When we now ask the REST API to say "hello" to our friend "Mr Pan" we're being presented with a nice greeting. Amazing! And more than enough of an end-to-end test if you don't even sport a web interface.

## 验收测试——你的功能工作正常吗？

测试金字塔越往上，越有可能需要从用户的角度来测试你所构建的功能是否能正常工作。你可以将应用视为黑盒，然后把测试关注点从这样的方式：

> The higher you move up in your test pyramid the more likely you enter the realms of testing whether the features you're building work correctly from a user's perspective. You can treat your application as a black box and shift the focus in your tests from

当我输入的值是 x 和 y 时，返回值应该是 z

> when I enter the values x and y, the return value should be z

转变为：

> towards

Given：在用户已经登陆

> given there's a logged in user

并且有一篇名为“bicycle”的文章的情况下

> and there's an article "bicycle"

When：当用户进入了“bicycle”这篇文章的详情页面

> when the user navigates to the "bicycle" article's detail page

并点击“添加到购物篮”按钮

> and clicks the "add to basket" button

Then：那么“bicycle”这篇文章应该出现在用户的购物车里

> then the article "bicycle" should be in their shopping basket

对于这样的测试，有时候你会听到像[功能性测试](https://en.wikipedia.org/wiki/Functional_testing)或者[验收测试](https://en.wikipedia.org/wiki/Acceptance_testing#Acceptance_testing_in_extreme_programming)这样的说法。偶尔会有人跟你说功能性和验收测试不是一回事。有时这些说法却又是一回事。人们可能对这些说法和定义陷入无尽的争论。这样的讨论经常会导致更多的混乱。

> Sometimes you'll hear the terms [functional test](https://en.wikipedia.org/wiki/Functional_testing) or [acceptance test](https://en.wikipedia.org/wiki/Acceptance_testing#Acceptance_testing_in_extreme_programming) for these kinds of tests. Sometimes people will tell you that functional and acceptance tests are different things. Sometimes the terms are conflated. Sometimes people will argue endlessly about wording and definitions. Often this discussion is a pretty big source of confusion.

我认为是这样的：无论如何，你总会需要从*用户的角度*而非仅仅从技术角度来测试软件是否正常工作。你怎么称呼这种测试并不是太重要，写测试本身才重要。称呼随便挑一个，保持后续术语一致，然后就开始编写这些测试吧。

> Here's the thing: At one point you should make sure to test that your software works correctly from a _user's_ perspective, not just from a technical perspective. What you call these tests is really not that important. Having these tests, however, is. Pick a term, stick to it, and write those tests.

人们时常提及 BDD 及一些相关的工具，它们可以用 BDD 风格来编写这类测试。BDD 或者 BDD 风格写出来的测试较易将你的思维从实现细节转向关注用户需求。你完全可以试一试。

> This is also the moment where people talk about BDD and tools that allow you to implement tests in a BDD fashion. BDD or a BDD-style way of writing tests can be a nice trick to shift your mindset from implementation details towards the users' needs. Go ahead and give it a try.

你甚至不必要采用已经十分成熟的 BDD 工具，例如[Cucumber](https://cucumber.io/)（虽然你也可以用）。有些断言库（如[chai.js](http://chaijs.com/guide/styles/#should)）也支持你使用`should-`风格的断言，这可以使你的测试读起来更 BDD 一些。即便你不用这样的库，精心组织一下代码也可以使测试专注在用户行为上。一些小巧的 helper 方法/函数就能让你做到这一点：

> You don't even need to adopt full-blown BDD tools like [Cucumber](https://cucumber.io/) (though you can). Some assertion libraries (like [chai.js](http://chaijs.com/guide/styles/#should) allow you to write assertions with `should`-style keywords that can make your tests read more BDD-like. And even if you don't use a library that provides this notation, clever and well-factored code will allow you to write user behaviour focused tests. Some helper methods/functions can get you a very long way:

```python
# 一个用Python写的验收测试示例

def test_add_to_basket():
    # given
    user = a_user_with_empty_basket()
    user.login()
    bicycle = article(name="bicycle", price=100)

    # when
    article_page.add_to_.basket(bicycle)

    # then
    assert user.basket.contains(bicycle)
```

验收测试可以有不同粒度的层次。大部分时候它们所处的测试级别都比较高，一般是直接从用户界面上测试服务。不过从技术上讲，验收测试不必总是写在测试金字塔的最高层。如果你的应用设计和场景允许你在低层级写验收测试，那就这样做。把它写成低层级测试要比写成高层级测试好。验收测试的概念——证明在用户视角看来，应用是正常工作的——是和测试金字塔完全契合的。

> Acceptance tests can come in different levels of granularity. Most of the time they will be rather high-level and test your service through the user interface. However, it's good to understand that there's technically no need to write acceptance tests at the highest level of your test pyramid. If your application design and your scenario at hand permits that you write an acceptance test at a lower level, go for it. Having a low-level test is better than having a high-level test. The concept of acceptance tests - proving that your features work correctly for the user - is completely orthogonal to your test pyramid.

## 探索测试

即便是最详尽的自动化测试，它也不是十全十美的。有时你总会在自动化测试里漏掉一些边缘情况。偶尔会出现一些难以单靠单元测试检测出来的 bug。某些质量问题甚至很难从自动化测试的视角被发现（诸如设计和可用性等）。即使对自动化测试抱着崇高的期望，一定程度的手工测试也是不可避免的。

> Even the most diligent test automation efforts are not perfect. Sometimes you miss certain edge cases in your automated tests. Sometimes it's nearly impossible to detect a particular bug by writing a unit test. Certain quality issues don't even become apparent within your automated tests (think about design or usability). Despite your best intentions with regards to test automation, manual testing of some sorts is still a good idea.

![图12](https://martinfowler.com/articles/practical-test-pyramid/exploratoryTesting.png)

_图 12：_ 使用探索性测试揪出构建流水线上没能发现的问题

_Figure 12: Use exploratory testing to spot all quality issues that your build pipeline didn't spot_

把[探索性测试](https://en.wikipedia.org/wiki/Exploratory_testing)纳入测试组合里。它是这样一种手工测试法：给予测试者自由，依赖他们的创造性来发现系统中的质量问题。定期花点时间，撸起袖子，试着对你的应用做些破坏性的操作，使其不能正常工作。开动你的破坏性思维，想方设法制造一些问题和错误。然后记录下你发现的所有东西，以供分析。其中要特别留意那些 bug、设计问题、很长的响应时间、缺失或有误导性的错误信息等一切会让作为用户的你感到恼怒的东西。

> Include [Exploratory Testing](https://en.wikipedia.org/wiki/Exploratory_testing) in your testing portfolio. It is a manual testing approach that emphasises the tester's freedom and creativity to spot quality issues in a running system. Simply take some time on a regular schedule, roll up your sleeves and try to break your application. Use a destructive mindset and come up with ways to provoke issues and errors in your application. Document everything you find for later. Watch out for bugs, design issues, slow response times, missing or misleading error messages and everything else that would annoy you as a user of your software.

好消息是，一般你发现的大部分问题都能写自动化测试来覆盖。写自动化测试来覆盖发现的这些 bug，有助于日后的回归测试中不会再重现同样的错误。并且，它能帮你在修复 bug 时，缩小 bug 产生根因的排查范围。

> The good news is that you can happily automate most of your findings with automated tests. Writing automated tests for the bugs you spot makes sure there won't be any regressions of that bug in the future. Plus it helps you narrowing down the root cause of that issue during bugfixing.

做探索性测试时，你可能会发现一些问题，但它们被构建流水线放过了。不用沮丧，这是关于流水线成熟度的绝佳反馈。收到反馈后，请采取必要的行动：思考一下，做点什么才能避免此类问题以后再次发生。也许是缺失了某一类自动化测试。也许是这个迭代的自动化测试做得马马虎虎，需要测试得更为透彻。也许有好用的新工具或方案可以让你的流水线日后避开这类问题。总之，采取行动是必要的，这样你的流水线，你整个的软件交付将变得越来越成熟。

> During exploratory testing you will spot problems that slipped through your build pipeline unnoticed. Don't be frustrated. This is great feedback on the maturity of your build pipeline. As with any feedback, make sure to act on it: Think about what you can do to avoid these kinds of problems in the future. Maybe you're missing out on a certain set of automated tests. Maybe you have just been sloppy with your automated tests in this iteration and need to test more thoroughly in the future. Maybe there's a shiny new tool or approach that you could use in your pipeline to avoid these issues in the future. Make sure to act on it so your pipeline and your entire software delivery will grow more mature the longer you go.

## 测试术语的困惑

讨论测试的不同分类总是十分困难。当我在讲单元测试的时候，可能与你理解的那个单元测试有些许差异；对于集成测试而言，可能差异会更大。一些人觉得，集成测试的覆盖面非常广，能测试到整个系统中的很多方面。对我而言它的范围则小得多，每个集成测试应仅仅测试一个与之集成的外部系统。有些人称这为*集成测试*，有些人则更喜欢称它们为*组件测试*，还有些人喜欢称为*服务测试*。很多人会争辩说，这三个术语是完全不同的东西。这里并无绝对的对与错。软件开发社区至今也没法给出关于测试术语的明确定义。

> Talking about different test classifications is always difficult. What I mean when I talk about unit tests can be slightly different from your understanding. With integration tests it's even worse. For some people integration testing is a very broad activity that tests through a lot of different parts of your entire system. For me it's a rather narrow thing, only testing the integration with one external part at a time. Some call them _integration tests_, some refer to them as _component tests_, some prefer the term _service test_. Even others will argue, that all of these three terms are totally different things. There's no right or wrong. The software development community simply hasn't managed to settle on well-defined terms around testing.

术语含义本身有模糊性，不必孜孜不倦于其中。叫端到端测试也好，广域栈测试也罢，功能性测试也行，都没问题。你认为的集成测试，可能和其他公司的人的认知也不同，这也没问题。当然，如果业界能有一些定义明确的术语并统一语言，那是再好不过了。可惜的是这件事尚未发生。而且在编写测试时会有很多细微差别，它们的范围更像是互相重叠而不是互相离散的，这使得保持术语的一致性更为艰难。

> Don't get too hung up on sticking to ambiguous terms. It doesn't matter if you call it end-to-end or broad stack test or functional test. It doesn't matter if your integration tests mean something different to you than to the folks at another company. Yes, it would be really nice if our profession could settle on some well-defined terms and all stick to it. Unfortunately this hasn't happened yet. And since there are many nuances when it comes to writing tests it's really more of a spectrum than a bunch of discrete buckets anyways, which makes consistent naming even harder.

找到适合你和你团队的术语，这就足够了。清晰理解不同类别测试的区别。团队要在测试命名上保持统一，要为每一类测试划分清晰的范围。只要能在团队内部达成一致（或甚至能上升到组织内部一致），你真的不需要关心别的事情了。[Simon Stewart](https://testing.googleblog.com/2010/12/test-sizes.html)在谈到他们在 Google 里的做法时有很好的总结。这篇文章完美展示了，为什么过于纠结名称和命名习惯本身不太值得。

> The important takeaway is that you should find terms that work for you and your team. Be clear about the different types of tests that you want to write. Agree on the naming in your team and find consensus on the scope of each type of test. If you get this consistent within your team (or maybe even within your organisation) that's really all you should care about. [Simon Stewart](https://testing.googleblog.com/2010/12/test-sizes.html) summed this up very nicely when he described the approach they use at Google. And I think it shows perfectly how getting too hung up on names and naming conventions just isn't worth the hassle.

## 把测试放到你的部署流水线上

如果你正在践行持续集成或者持续交付的实践，那么你会有一条[部署流水线](https://martinfowler.com/bliki/DeploymentPipeline.html)来在每一次提交改动时运行自动化测试。通常这个流水线会被分成几个阶段，它们会逐步建立起让你把软件部署到生产环境的自信。听了这么多不同类型的测试，你可能想进一步了解它们在部署流水线中应如何放置。要回答这个答案，你需要思考一下持续交付（实际上是[极限编程](http://www.extremeprogramming.org/values.html)和敏捷软件开发的核心价值观之一）的其中一项核心价值观：**快速反馈**。
    
> If you're using Continuous Integration or Continuous Delivery, you'll have a [Deployment Pipeline](https://martinfowler.com/bliki/DeploymentPipeline.html) in place that will run automated tests every time you make a change to your software. Usually this pipeline is split into several stages that gradually give you more confidence that your software is ready to be deployed to production. Hearing about all these different kinds of tests you're probably wondering how you should place them within your deployment pipeline. To answer this you should just think about one of the very foundational values of Continuous Delivery (indeed one of the core [values of Extreme Programming](http://www.extremeprogramming.org/values.html) and agile software development): **Fast Feedback**.

好的构建流水线能在构建失败时第一时间通知你。你一定不想等了一小时后发现，最新的改动因为几个简单的单元测试挂掉了。真实的情况是，如果流水线真的需要这么长时间才能给你反馈，你可能早就已经等不及回家了。你可以将运行快速的测试放到流水线更靠前的阶段执行，这样你就可以在数秒钟或几分钟内得到反馈。相对地，把那些运行时间较长的测试——通常是覆盖范围更广的测试——放到流水线中靠后的阶段执行，以免影响你从运行快速的测试中更快地获取反馈。你可能发现了，部署流水线中不同阶段的区分不是由测试类型决定的，而是取决于测试的运行速度和覆盖范围。记住这一点后，将一些覆盖范围有限、运行速度又很快的集成测试与单元测试一起放在同一个阶段，就是很合理的决策了。你的目的是要更快速地获得反馈，而不是在各种类型的测试中间划出清晰的界线来。

> A good build pipeline tells you that you messed up as quick as possible. You don't want to wait an hour just to find out that your latest change broke some simple unit tests. Chances are that you've probably gone home already if your pipeline takes that long to give you that feedback. You could get this information within a matter of seconds, maybe a few minutes by putting the fast running tests in the earlier stages of your pipeline. Conversely you put the longer running tests - usually the ones with a broader scope - in the later stages to not defer the feedback from the fast-running tests. You see that defining the stages of your deployment pipeline is not driven by the types of tests but rather by their speed and scope. With that in mind it can be a very reasonable decision to put some of the really narrowly-scoped and fast-running integration tests in the same stage as your unit tests - simply because they give you faster feedback and not because you want to draw the line along the formal type of your tests. 

## 避免测试重复

现在你已经理解了为何你需要为软件编写不同类型的测试，但是这还有一个陷阱你需要避开：金字塔不同层级进行了重复测试。虽然你本能会说测试太多没啥问题，但我向你保证，会有问题。测试组合中每一个测试都有一定的成本，它们不是免费的。编写和维护测试都要花费时间。阅读和理解其他人写的测试也要花时间。当然，运行这些测试也要费时间。

> Now that you know that you should write different types of tests there's one more pitfall to avoid: duplicating tests throughout the different layers of the pyramid. While your gut feeling might say that there's no such thing as too many tests let me assure you, there is. Every single test in your test suite is additional baggage and doesn't come for free. Writing and maintaining tests takes time. Reading and understanding other people's test takes time. And of course, running tests takes time.

对于产品代码，你应该力争简洁，杜绝重复。在实现测试金字塔时，你也应该牢记这两条基本法则：

> As with production code you should strive for simplicity and avoid duplication. In the context of implementing your test pyramid you should keep two rules of thumb in mind:

1.  如果一个更高层级的测试发现了一个错误，并且底层测试全都通过了，那么你应该写一个低层级测试去覆盖这个错误
2.  竭尽所能把测试往金字塔下层赶

> 1.  If a higher-level test spots an error and there's no lower-level test failing, you need to write a lower-level test
> 2.  Push your tests as far down the test pyramid as you can

第一条法则很重要，这是因为低层级测试让你更容易缩小错误的范围，并且隔离掉大部分上下文把错误重现。在调试手头上的问题时，低层级测试运行起来更快，没有太多冗余的东西。同时它们也是很好的回归测试。第二条法则很重要，它能保持测试组合快速运行。如果你已经在低层级测试里覆盖了所有情况，那么再维护一个高层级的测试就没有必要了。因为这并不能给你就软件的正常工作带来更多的信心。如果有许多无效的测试，它也会让你的日常工作很恼火。测试组合会因此拖慢节奏，当你改变代码行为时就需要改更多的测试。

> The first rule is important because lower-level tests allow you to better narrow down errors and replicate them in an isolated way. They'll run faster and will be less bloated when you're debugging the issue at hand. And they will serve as a good regression test for the future. The second rule is important to keep your test suite fast. If you have tested all conditions confidently on a lower-level test, there's no need to keep a higher-level test in your test suite. It just doesn't add more confidence that everything's working. Having redundant tests will become annoying in your daily work. Your test suite will be slower and you need to change more tests when you change the behaviour of your code.

或者让我们这样总结一下：如果写一个更高层级测试能给你带来更多的信心，那就写高层的测试。给一个 Controller 类写单元测试可以测试它内部的逻辑。不过它还是没法告诉你这个 Controller 提供的 REST 路径是否能真正响应 HTTP 请求。那你可以上移一下测试层级，多写一个测试来测试这个点——就测这个点，不能更多了。你不需要再测试所有的条件分支和边缘场景，因为低层级测试已经覆盖到了。保证高层级测试仅仅关注低层级测试覆盖不到的地方。

> Let's phrase this differently: If a higher-level test gives you more confidence that your application works correctly, you should have it. Writing a unit test for a Controller class helps to test the logic within the Controller itself. Still, this won't tell you whether the REST endpoint this Controller provides actually responds to HTTP requests. So you move up the test pyramid and add a test that checks for exactly that - but nothing more. You don't test all the conditional logic and edge cases that your lower-level tests already cover in the higher-level test again. Make sure that the higher-level test focuses on the part that the lower-level tests couldn't cover.

我对于失去了价值的测试很严格，必须消灭掉它们。我会删掉已经被低层级测试覆盖完全的高层级测试（考虑到它们不会再提供额外的价值了）。我尽可能用低层级测试取代高层级测试。有时候，这会有些困难，尤其是你知道设计测试本身就很艰难。警惕沉没成本的思维陷阱，果断摁下删除键。没有理由在不再提供价值的测试上浪费宝贵时间。

> 译者注：[沉没成本](https://zh.wikipedia.org/wiki/%E6%B2%89%E6%B2%A1%E6%88%90%E6%9C%AC)是一个经济学概念，沉没成本谬误在这里指的是不忍删除花了时间精力撰写的测试

> I'm rigorous when it comes to eliminating tests that don't provide any value. I delete high-level tests that are already covered on a lower level (given they don't provide extra value). I replace higher-level tests with lower-level tests if possible. Sometimes that's hard, especially if you know that coming up with a test was hard work. Beware of the sunk cost fallacy and hit the delete key. There's no reason to waste more precious time on a test that ceased to provide value.

## 整洁测试代码

和写代码一样，良好整洁的测试代码同样需要悉心照料。在你于自动化测试之路上继续前进之前，我有些写好可维护测试代码的窍门想告诉你：

> As with writing code in general, coming up with good and clean test code takes great care. Here are some more hints for coming up with maintainable test code before you go ahead and hack away on your automated test suite:

1.  测试代码跟生产代码一样重要。要对它们赋予同等的关注和照顾。*“这只是测试代码”*不能成为你写出邋遢代码的借口
2.  一个测试只测试一个分支。这能帮你的测试保持短小，容易理解
3.  *“arrange, act, assert”*或者*“given, when, then”*等口诀有助于你写出结构良好的测试
4.  可读性很重要。不要过于追求 DRY。如果能提高可读性，重复有时候也是可以接受的。尝试在[DRY 和 DAMP](https://stackoverflow.com/questions/6453235/what-does-damp-not-dry-mean-when-talking-about-unit-tests)之间寻找好平衡
5.  如果对于重复有疑惑，试试用[Rule of Three](https://blog.codinghorror.com/rule-of-three/)法则来决定是不是要重构。_重构之前先试用一下_

> 1.  Test code is as important as production code. Give it the same level of care and attention. _"this is only test code"_ is not a valid excuse to justify sloppy code
> 2.  Test one condition per test. This helps you to keep your tests short and easy to reason about
> 3.  _"arrange, act, assert"_ or _"given, when, then"_ are good mnemonics to keep your tests well-structured
> 4.  Readability matters. Don't try to be overly DRY. Duplication is okay, if it improves readability. Try to find a balance between [DRY and DAMP](https://stackoverflow.com/questions/6453235/what-does-damp-not-dry-mean-when-talking-about-unit-tests) code
> 5.  When in doubt use the [Rule of Three](https://blog.codinghorror.com/rule-of-three/) to decide when to refactor. _Use before reuse_

## 结论

好了！我知道这是一篇非常漫长并且艰深的文章，它解释了为什么我们需要测试，以及如何对软件进行测试的问题。好消息是，这篇文章提供的信息经得起时间推敲，无论你在构建什么样的软件都能适用。不管你是工作在一个微服务项目上，还是 IoT 设备上，抑或是手机应用或者网页应用，这篇文章提供的观点应该都有章可寻。

> That's it! I know this was a long and tough read to explain why and how you should test your software. The great news is that this information is pretty timeless and independent of what kind of software you're building. It doesn't matter if you're working on a microservices landscape, IoT devices, mobile apps or web applications, the lessons from this article can be applied to all of these.

我希望这篇文章能对你有些帮助。有兴趣你可以去[示例代码](https://github.com/hamvocke/spring-testing)看看，把这里介绍的一些概念纳入到你的测试组合中。想拥有一套稳固的测试组合确实需要付出努力。但长远来看，它们是会给你回报的，它们会给作为开发者的你带来更多清净。相信我。

> I hope that there's something useful in this article. Now go ahead and check out the [sample code](https://github.com/hamvocke/spring-testing) and get some of the concepts explained here into your testing portfolio. Having a solid test portfolio takes some effort. It will pay off in the longer term and it will make your live as a developer more peaceful, trust me.

### 致谢

感谢 Clare Sudbery, Chris Ford, Martha Rohte, Andrew Jones-Weiss David Swallow, Aiko Klostermann, Bastian Stein, Sebastian Roidl 及 Birgitta Böckeler 为本文的早期手稿提供反馈和建议。感谢 Martin Fowler 的建议，洞见和支持。

> Thanks to Clare Sudbery, Chris Ford, Martha Rohte, Andrew Jones-Weiss David Swallow, Aiko Klostermann, Bastian Stein, Sebastian Roidl and Birgitta Böckeler for providing feedback and suggestions to early drafts of this article. Thanks to Martin Fowler for his advice, insights and support.
