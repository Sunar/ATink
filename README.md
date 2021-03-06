# ATink
A bank application for learning purposes

<h2>Installing and running</h2>

First of all, pull the project into your computer by clicking "Get from VCS" button on Android Studio welcome screen. Paste the url of current project.

(Optional) You may need to configure gradle jdk in order to run the app properly.
To do so, go to Preferences -> Build, Execution, Deployment -> Gradle -> Gradle JDK.
Set Embedded JDK with version 11+

If you are able to run the app and see its screen, it means you did everything correctly.

<h2>Architecture</h2>

We use Clean Architecture with MVVM

![image](https://user-images.githubusercontent.com/7592027/163554976-be36e746-cfae-41a5-89be-45ecdda91f85.png)

    Views & ViewModels  || Interactors, UseCases & Entities || Repositories
    
So our code source is divided into 3 main packages: *Presentation, Domain, Data.*

Our independent package is *Domain* with business rules, so nothing in that package knows about files from *Presentation & Data*.

1. *Domain* contains UseCases & Business Entities (some models and critical functions) & interfaces of repositories.<br />
2. *Data* contains Repositories that implement interfaces based in *Domain*, server DAOs, DTOs, all work with server and DB.<br />
3. *Presentation* contains Views(Activities & Fragments) and ViewModels. Additionally, we put here CustomViews, utils for UI etc.

<h2>Depedency Injection</h2>

According to Clean Architecture, we create a UseCase with a repository interface argument. <br />
For example LoginUserUseCase(private val userRepository: IUserRepository)<br />

Our IUserRepository based inside *Domain* package, but how do we provide implementation from *Data* package into that UseCase?
The answer is <b>Dependency Injection</b>. The most common library is Dagger 2. It cannot be counted as the best library because it has own pros and cons of usage. But for learning purposes, it will be better to start work with the most famous library.

We create a component, add some modules and connect them to that component. So when Dagger tries to create a new object with an argument, and that argument is class or interface, Dagger searches its creation or implementation among modules. When Dagger finds it, Dagger uses it to create that object.

It's the most common structure for that tech stack (MVVM + Clean + Dagger 2):

    RepositoryImpl (Class)
          ???
    IRepository (Interface)
          ???
    UseCase(repo: IRepository)
          ???
    ViewModel(useCase: UseCase)
          ???
    Fragment(without constructors, injections happen inside)

As you see above, when we try to inject a ViewModel inside a Fragment, Dagger uses ViewModel(useCase: UseCase) constructor. It means that Dagger needs a UseCase object to create that ViewModel. Dagger searches UseCase creation and finds UseCase(repo: IRepository) constructor. It searches further and finds how to implement that interface with RepositoryImpl object.
Usually RepositoryImpl goes with some arguments to access Web & Database, but I skip it to show you the main idea of DI. So when Dagger finds out how to implement that IRepository, we can create UseCase -> we can create ViewModel -> we can inject that ViewModel inside the Fragment. 

<h2>Code Style</h2>

We follow a code style recommended by Google & Kotlin, but extended by RedMadRobotTeam:
https://github.com/RedMadRobot/kotlin-style-guide#readme

<h2>Work with Git</h2>

In order to accomplish your task, it's required to make all changes on a separated branch and merge it into the develop branch. 

Every merge request should contain less than 300 changed lines.

What you should do step by step after receiving a new task:<br/>

1. Checkout local dev branch and pull remote changes.
2. Create your own branch ANDI-number (the number of current task).
3. Make commits and push them into remote repository (new remote branch with same name ANDI-number will be created).
4. On GitHub make a new MR (merge request) ANDI-number -> dev. Set your teammates as Reviewers and set the tech lead as Assignee.
5. Wait until pipelines successfully finish and then ask your teammates to review your merge request. 
6. Get 2 approves from your Reviewers. If there is a comment left on Github, first of all you should ensure it's correct, otherwise discuss it with a reviewer who left that arguable comment. Make changes to fix those commented parts of code and send your MR to review again. Afterward, when you get 2 approves, send approve request to the tech lead.
7. After the tech lead's approve, you can safely merge your branch into dev.
