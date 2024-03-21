# Lecture 11a: Programming Languages

There are many angles from which you can analyze programming languages.
We will take a look at a few of them, but given that we are interested in programming languages from a web-development perspective, this is not a comprehensive analysis.

Our main concern is to be able to choose between different programming languages for a given task - just like we choose between other tools.
In that regard we also have to take the eco-systems (i.e., tools, frameworks, community etc.) that exists for the programming language in question into account..


## A Brief History of Programming Languages for the Web

As the Internet and the World Wide Web became mainstream in the late 1990s, the need for systems that could dynamically generate content became apparent.
The web originally consisted of static content, which was fine for its earliest application: sharing documents between universities.

At this time, most companies ran servers based on Microsoft Windows NT, IBM OS/2, Novel Netware etc.
These server systems were ill suited for use on the Internet, and they were prohibitively expensive because of their per-user licensing models.

While especially Microsoft tried to establish their platform as viable systems for Internet web serving, it was the *LAMP* stack that would revolutionize the web.
The LAMP stack consists of the Linux operating system, the Apache web server, MySQL database, and the PHP programming language.
These products all have free and open source licensing models, meaning service providers could build web servers only paying for hardware and time spent.
Furthermore, Linux could handle large amounts of traffic, Apache supported the *host header* protocol, allowing a large number of domains to be served on the same IP address and server.
Finally, service providers could let their users run dynamic web applications using PHP, as this was just a matter of dumping files in a file structure, unlike many other programming languages.

Another popular way of running code on the web server was via *CGI*, Common Gateway Interface.
CGI can be written in almost any programming language, but users can not install their own CGI programs unless they have shell access to the host computer, something that is not a good idea when many users share the same host computer.

On the client side there was a need to create a better user experience, and for that running client-side code was necessary.
This was know as *DHTML*, Dynamic HTML, but due to the *browser wars* several incompatible technologies existed.

Sun Microsystems developed *Java Applets*, written in Java.
These Java applets was very much isolated from the rest of the page, not ideal for manipulating the *DOM* (Document Object Model).
Also, running Java applets in the browser required that the user had a Java Runtime Environment installed, including browser extensions that caused a lot of security problems.

Microsoft launched their own incompatible version of Java for their Internet Explorer browser, causing a long legal fight with Sun Microsystems (later Oracle).

Microsoft also launched ActiveX, a Windows-only technology that allowed running code from the client PC (what could possibly go wrong?), but also code restricted to the browser.

Meanwhile, Netscape had launched ECMAScript (later JavaScript for marketing reasons).
This only worked in the Netscape browser, but Microsoft quickly adopted JavaScript and implemented their own proprietary APIs, causing incompatibility with Netscape JavaScript (see a pattern here?).

Another technology that deserves mention is Flash, developed by FutureWare, later bought by Adobe.
Flash allowed rich media in the browser at a time when animated GIFs were the pinnacle of web technology.
Flash caused many problems due to extreme CPU and memory usage, and a very poor security track record.

This caused Apple to make the decision of not supporting Flash on the iPhone - a decision that lead to the demise of Flash.

At present, Microsoft no longer plays a dominating role in the browser market.
However, Google is now dominant with their Chrome browser, and they seem eager to repeat Microsoft's attempts at using proprietary implementations to monopolize the market.

The book [The Future of the Internet: And How to Stop It](https://dash.harvard.edu/bitstream/handle/1/4455262/Zittrain_Future+of+the+Internet.pdf?sequence=1) by Jonathan Zittrain is highly recommended and essential reading for any web developer.
At the very least you should watch this video: [Jonathan Zittrain: The Future of the Internet: And How to Stop It](https://www.youtube.com/watch?v=o7UlYTFKFqY).


## The Current State of Web Programming Languages

At this point in time, the JavaScript programming language is the de facto standard for front-end code, as it has until recently been the only programming language widely supported by major browsers.

JavaScript is considered a very poor programming language by many computer scientists and engineers, but it remains one of the most commonly used programming languages, and today it is also possible to develop back-end code in JavaScript.
Many attempts has been made to alleviate the poor quality of the JavaScript programming language, such as CoffeeScript, TypeScript and others, that allow the user to write code in a supposedly better programming language, and then compile to JavaScript.

However, the need for a better programming language has also made way for Web Assembly, which allows for code to be written in *real* programming languages, such as C, C++, Rust, Go, etc., and then compile to byte code that can be run on modern browsers.
At this point JavaScript is needed to actually load the code in the browser, but Web Assembly does not depend on JavaScript otherwise.
You should expect Web Assembly to play a major role, and even replace JavaScript, in the not too distant future.

On the back-end side things are very different.
Web frameworks exists for all major programming languages, so back-end systems can be written in many more programming languages such as Python, Ruby, Swift, Go, Rust, C#, C, C++, etc.

Choosing a back-end system is to a very high degree a choice of a web framework rather than a programming language - we will get into this in next week's lecture.


## Types of Programming Languages

Making a taxonomy of programming languages can be done from many perspectives - let's briefly look at some of them.


### Memory Management

The memory management of a programming language has great impact on how secure code written in that language is.
It's not really that the languages are insecure, it's that programmers are not good enough at using them.

Older languages such as C, C++, Objective C, etc., often rely on the programmer to handle memory allocation and deallocation.
This is quite complicated though - as an example Microsoft has estimated that errors in allocation and deallocation of memory, and under- and overflow handling of memory counts for 90% of the security vulnerabilities in products like Microsoft Windows, Office, etc.
More modern languages typically handles some of these issues via garbage collection or object reference counting, avoiding many security problems.


### Interpreted vs Compiled

We often distinguish between interpreted and compiled languages.

With interpreted languages such as JavaScript, Python, Ruby and others, the executable code consists of the source code itself (in plain text).
This is convenient in development, because it is easy to work interactively with interpreted languages.

Compiled languages on the other hand, must be compiled into object files and then linked into machine code.
The upside here is that compiled code is potentially orders of magnitude more efficient, and the compilation process itself will catch many errors in the code.
However, compiled code is dependent on the system architecture, so code need to be compiled for different CPUs, such as ARM or Intel, and different operating systems, whereas interpreted code can be used on any system architecture as long as it abstracts things that are different between them, e.g., file paths in Windows vs UNIX like systems.

Between the two approaches we have JIT - Just In Time compilation.
JIT is basically an optimization of interpreted languages, where a compilation takes place just before the code is executed.
The compiled code can be cached and reused.

Neither interpreted or compiled languages are better than the other, they both have their strengths and weaknesses.
The main distinction here is the trade off of run time performance vs development time performance.


### Programming Language Paradigms

Programming languages follow several different programming paradigms.
The most notable are:

- Structured programming: in structured programming languages, code is organized into *procedures* and *functions*. Procedures and functions do not have a state, so data is always passed as arguments to the procedure or function. Structured programming languages was a big step forward from prior paradigms where labels or line numbers was used as targets for GOTO or JMP (jump) instructions. Examples of structured programming languages are: C, Pascal, COBOL, Modula-2, Algol.
- Object Oriented programming: in object oriented programming, code and data are organized together in *objects*, so objects have state. Examples of object oriented languages: JavaScript, Java, C#, C++, Objective C, Smalltalk.
- Functional programming: in functional programming, code is organized into functions. These functions must not have *side-effects*, but only return data. One advantage of functional languages is that it is often possible to mathematically prove the correctness of a functional program. Examples of functional programming languages: Lisp, Erlang, Haskell, Clojure, Elm.
- Multi-paradigm programming: some languages have strong support for several paradigms. Examples of multi-paradigm languages: Python, Ruby.


### Imperative vs Declarative

Another useful distinction is imperative vs declarative.

While most *general purpose* programming languages are *imperative*, i.e., we give instruction `do_this()` and `do_that()`, two *declarative* languages are of special interest in web development.
These are both domain specific languages rather than general purpose languages.

The first is *SQL* (Structured Query Language), which is not a structured language in the sense we discussed in Programming Language Paradigms.
In declarative languages, rather than giving imperative instructions, we *target* elements to manipulate.
With SQL the elements we target is the data.

CSS (Cascading Style Sheets) is another declarative language.
In CSS we target elements in the document object model, and again manipulate these elements.


## Programming Language Eco-Systems


Programming languages do not exist in a vacuum, rather they are dependent on the eco-system that exists around the language.

This eco-system consists of tools and communities, and these are some of the most important parts of the eco-system:

- Test tools: software suites for testing the software is (as you should know by now) essential to be able to create quality software.
- Lint tools: again a part of the software quality assurance process, code linting tools are needed to automate adherence to standards.
- Optimization tools: tools to optimize code run time or resource usage are important to create high performance code, and to find certain types of errors.
- Debugging tools: debugging tools allow the developer to inspect the memory and run the code step-by-step to correct errors.
- Profiling tools: profiling tools help fix run time and memory related issues such as memory leaks.
- Frameworks: the existence of frameworks for various problem domains is also essential to the applicability of a programming language.
- Community: communities around programming languages allow users to get help and share experiences.

## The Killer Application

The success or failure of a programming language rarely have much to do with how good the programming language is.

The one factor that is probably most significant is whether there is a *killer application* for the language, and whether this killer application is sufficiently better that what development teams can find for other languages.

Examples of killer applications for various programming languages:

- Ruby on Rails: Rails is a great web development framework for the Ruby programming language. However, Ruby has failed to establish itself long term, because while Rails is a great framework, it is pretty much the only use of the Ruby programming language with significant adoption.
- Apple platform development for Objective C and Swift: creating *real* native programs for Apple platforms require the use of Objective C or Swift. Objective C is a beautiful and high performant language, but a bit difficult to learn. For many years Objective C was the only language for native Apple development, and Objective C had very little adoption outside the Apple eco-system. This made it difficult to attract enough proficient Objective C programmers. Apple has introduced the Swift programming language few years ago, and to make way for a higher degree of adoption, the Swift language has been made open source, and made available for Linux. This has resulted in several web frameworks built in Swift.
- Running in the browser: while newer versions of JavaScript has improved, it is not known for being a great language. JavaScript is highly adopted for one single reason: for many years it has been the only language that could run front-end code in the browser.
- Systems programming: C is the language of choice for writing operating systems, and it was made for this purpose. Every major operating system in use today is written in C (and some C++ or Objective C). C is known for being second to only assembler programming in run time performance.


## Summary

It is hopefully clear by now that there are many considerations to be taken into account when choosing a programming language for a project.
We should avoid automatically choosing our favorite language if it is actually not the best tool for the job.

No programming language is better than the eco-system that surrounds it.
The eco-system consists of tools, communities, and as we shall see next week: libraries, frameworks, and content management systems.
When choosing a programming language we limit ourselves as to what the eco-system has to offer.


## Assignments

For the assignments below, choose a web development project you have been involved in recently.

> **Assignment 11a.1:** Who made the choice of programming language, and how was that decision made?

> **Assignment 11a.2:** In your experience, was the choice of language the right choice? Why or why not?

> **Assignment 11a.3:** Make a requirement specification for the choice of programming language in the project, and evaluate five programming languages against the requirements.
