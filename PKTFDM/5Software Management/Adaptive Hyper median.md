---
title: "Adaptive hypermedia - Wikipedia"
source: "https://en.wikipedia.org/wiki/Adaptive_hypermedia"
author:
  - "[[Contributors to Wikimedia projects]]"
published: 2007-02-02
created: 2025-09-23
description:
tags:
  - "clippings"
---
**Adaptive hypermedia** (**AH**) uses [hypermedia](https://en.wikipedia.org/wiki/Hypermedia "Hypermedia") which is adaptive according to a *[user model](https://en.wikipedia.org/wiki/#User_model)*. In contrast to regular hypermedia, where all users are offered the same set of [hyperlinks](https://en.wikipedia.org/wiki/Hyperlink "Hyperlink"), adaptive hypermedia (AH) tailors what the user is offered based on a model of the user's goals, preferences and knowledge, thus providing links or content most appropriate to the current user.

## Background

Adaptive hypermedia is used in educational hypermedia,on-line information and help systems, as well as institutional information systems.Adaptive educational hypermedia tailors what the learner sees to that learner's goals, abilities, needs, interests, and [knowledge](https://en.wikipedia.org/wiki/Knowledge "Knowledge") of the subject, by providing hyperlinks that are most relevant to the user in an effort to shape the user's [cognitive load](https://en.wikipedia.org/wiki/Cognitive_load "Cognitive load"). The teaching tools "adapt" to the learner. On-line information systems provide reference access to information for users with a different knowledge level of the subject.

An adaptive hypermedia system should satisfy three criteria: it should be a [hypertext](https://en.wikipedia.org/wiki/Hypertext "Hypertext") or [hypermedia](https://en.wikipedia.org/wiki/Hypermedia "Hypermedia") system, it should have a [user model](https://en.wikipedia.org/w/index.php?title=Standard_user_model&action=edit&redlink=1 "Standard user model (page does not exist)") and it should be able to [adapt](https://en.wikipedia.org/wiki/Adaptation "Adaptation") the hypermedia using the model.

A [semantic](https://en.wikipedia.org/wiki/Semantics "Semantics") distinction is made between *[adaptation](https://en.wikipedia.org/wiki/Adaptation "Adaptation")*, referring to system-driven changes for [personalisation](https://en.wikipedia.org/wiki/Personalisation "Personalisation"), and *adaptability*, referring to user-driven changes. One way of looking at this is that adaptation is automatic, whereas adaptability is not. From an [epistemic](https://en.wikipedia.org/wiki/Epistemic "Epistemic") point of view, adaptation can be described as analytic, [a-priori](https://en.wikipedia.org/wiki/A_priori_and_a_posteriori "A priori and a posteriori"), whereas adaptability is synthetic, [a-posteriori](https://en.wikipedia.org/wiki/A-posteriori "A-posteriori"). In other words, any adaptable system, as it "contains" a human, is by default "intelligent", whereas an adaptive system that presents " [intelligence](https://en.wikipedia.org/wiki/Intelligence "Intelligence") " is more surprising and thus more interesting.[

## Architecture

The system categories in which user modelling and adaptivity have been deployed by various researchers in the field share an underlying architecture. The conceptual structure for adaptive systems generally consists of interdependent components: a [user model](https://en.wikipedia.org/w/index.php?title=Standard_user_model&action=edit&redlink=1 "Standard user model (page does not exist)"), a [domain model](https://en.wikipedia.org/wiki/Domain_model "Domain model") and an [interaction model](https://en.wikipedia.org/wiki/Interaction_model "Interaction model").

### User model

The user model is a representation of the knowledge and preferences which the system 'believes' a user (which may be an individual, a group of people or non-human agents) possesses. It is a knowledge source which is separable by the system from the rest of its knowledge and contains explicit assumptions about the user. Knowledge for the user model can be acquired implicitly by making inferences about users from their interaction with the system, by carrying out some form of test, or from assigning users to generic user categories usually called ' [stereotypes](https://en.wikipedia.org/wiki/Stereotypes "Stereotypes") '. The student model consists of a personal profile (which includes static data, e.g., name and password), cognitive profile (adaptable data such as preferences), and a student knowledge profile. Systems may adapt, depending on user features such as:

- goals (a feature related with the context of a user's work in hypermedia)
- knowledge (knowledge of the subject represented in the hyperspace)
- background ( all the information related to the user's previous experience outside the subject of the hypermedia system which is relevant enough to be considered)
- hyperspace experience (how familiar is the user with the structure of the hyperspace and how easily can the user navigate it)
- preferences (the user can prefer some nodes and links over others and some parts of a page over others).

### Domain model

The [domain model](https://en.wikipedia.org/wiki/Domain_model "Domain model") defines the aspects of the application which can be adapted or which are otherwise required for the operation of the adaptive system. The domain model contains several concepts that stand as the backbone for the content of the system. Other terms which have been used for this concept include content model, application model, system model, device model and task model. It describes educational content such as information pages, examples, and problems. The simplest content model relates every content item to exactly one domain concept (in this model, this concept is frequently referred to as a domain topic). More advanced content models use multi-concept indexing for each content item and sometimes use roles to express the nature of item-concept relationship. A cognitively valid domain model should capture descriptions of the application at three levels, namely:

- The task level which makes the user aware of the system purpose.
- The logical level which describes how something works.
- The physical level which describes how to do something.

Each content concept has a set of topics. Topics represent individual pieces of knowledge for each domain and the size of each topic varies in relation to the particular domain. Additionally, topics are linked to each other forming a [semantic network](https://en.wikipedia.org/wiki/Semantic_network "Semantic network"). This network is the structure of the knowledge domain.

The interaction or adaptation model contains everything which is concerned with the relationships which exist between the representation of the users (the user model) and the representation of the application (the domain model).It displays information to the user based on his or her cognitive preferences. For instance, the module will divide a page's content into chunks with conditions set to only display to certain users or preparing two variants of a single concept page with a similar condition. The two main aspects to the interaction model are capturing the appropriate raw data and representing the inferences, adaptations and evaluations which may occur.

Content-level and link-level adaptation are distinguished as two different classes of hypermedia adaptation; the first is termed *adaptive presentation* and the second, *adaptive navigation support*

#### Adaptive presentation

The idea of various adaptive presentation techniques is to adapt the content of a page accessed by a particular user to current knowledge, goals, and other characteristics of the user. For example, a qualified user can be provided with more detailed and deep information while a novice can receive additional explanations. Adaptive text presentation is the most studied technology of hypermedia adaptation. There are a number of different techniques for adaptive text presentation.

The idea of adaptive navigation support techniques is to help users to find their paths in hyperspace by adapting the way of presenting links to goals, knowledge, and other characteristics of an individual user. This area of research is newer than adaptive presentation, a number of interesting techniques have been already suggested and implemented. We distinguish four kinds of link presentation which are different from the point of what can be altered and adapted:

- Local non-contextual links – This type includes all kinds of links on regular hypermedia pages which are independent from the content of the page.
- Contextual links or "real hypertext" links – This type comprises "hotwords" in texts, "hot spots" in pictures, and other kinds of links which are embedded in the context of the page content and cannot be removed from it.
- Links from index and content pages – An index or a content page can be considered as a special kind of page which contains only links.
- Links on local maps and links on global hyperspace maps – Maps usually graphically represent a hyperspace or a local area of hyperspace as a network of nodes connected by arrows.

### Methods

Adaptation methods are defined as generalizations of existing adaptation techniques. Each method is based on a clear adaptation idea which can be presented at the conceptual level.

#### Content adaptation

- **additional explanations** – hides parts of information about a particular concept which are not relevant to the user's level of knowledge about this concept,
- **prerequisite explanations** – before presenting an explanation of a concept the system inserts explanations of all its prerequisite concepts which are not sufficiently known to the user,
- **comparative explanations** – if a concept similar to the concept being presented is known, the user gets a comparative explanation which stress similarities and differences between the current concept and the related one,
- **explanation variants** – assumes that showing or hiding some portion of the content is not always sufficient for the adaptation because different users may need essentially different information,
- **sorting** – fragments of information about the concept are sorted from information which is most relevant to user's background and knowledge to information which is least relevant.

#### Link adaptation

- **global guidance** – the system suggests navigation paths on a global scale,
- **local guidance** – the system suggests the next step to take, for instance through a "next" or "continue" button,
- **local orientation support** – the system presents an overview of a part of the (link) structure of the hyperspace,
- **global orientation support** – the system presents an overview of the whole (link) structure of the hyperspace,
- **managing personalized views in information spaces** – each view may be a list of links to all pages or sub-parts of the whole hyperspace which are relevant for a particular working goal.

### Techniques

Adaptation techniques refer to methods of providing adaptation in existing AH systems.

#### Content adaptation

- **conditional text** – with this technique, all possible information about a concept is divided into several chunks of texts. Each chunk is associated with a condition on the level of user knowledge represented in the user model. When presenting the information about the concept, the system presents only the chunks where the condition is true.
- **stretchtext** – turns off and on different parts of the content according to the user knowledge level.
- **page variants** – the most simple adaptive presentation technique. With this technique, a system keeps two or more variants of the same page with different presentations of the same content.
- **fragment variants** – The system stores several variants of explanations for each concept and the user gets the page which includes variants corresponding to his or her knowledge about the concepts presented in the page
- **frame-based techniques** – With this technique all the information about a particular concept is represented in form of a frame. Slots of a frame can contain several explanation variants of the concept, links to other frames, examples, etc. Special presentation rules are used to decide which slots should be presented to a particular user and in which order.

#### Link adaptation

- **direct guidance** – the "next best" node for the user to visit is shown, e.g. through a "next" or "continue" button,
- **link sorting** – all the links on a particular page are sorted according to the user model and to some goal-oriented criteria: the more towards the top of the page, the more relevant the link is,
- **link hiding** – hiding links to "non-relevant" pages by changing the color of the anchors to that of normal text,
- **link annotation** – to augment the link with some form of comment which tells the user more about the current state of the pages to which the annotated links refer,
- **link disabling** – the "link functionality" of a link is removed,
- **link removal** – link anchors for undesired links (non-relevant or not yet ready to read) are removed,
- **map adaptation** – the content and presentation of a map of the link structure of the hyperspace is adapted.

Authoring adaptive hypermedia uses designing and creation processes for content, usually in the form of a resource collection and [domain model](https://en.wikipedia.org/wiki/Domain_model "Domain model"), and adaptive behaviour, usually in the form of [IF-THEN rules](https://en.wikipedia.org/wiki/Conditional_\(programming\) "Conditional (programming)"). Recently, adaptation languages have been proposed for increased generality. As adaptive hypermedia adapts at least to the user, authoring of AH comprises at least a [user model](https://en.wikipedia.org/w/index.php?title=Standard_user_model&action=edit&redlink=1 "Standard user model (page does not exist)"), and may also include other aspects.

### Issues

Authoring of adaptive hypermedia was long considered as secondary to adaptive hypermedia delivery. This was not surprising in the early stages of adaptive hypermedia, when the focus was on research and expansion. Now that adaptive hypermedia itself has reached a certain maturity, the issue is to bring it out to the community and let the various [stakeholders](https://en.wikipedia.org/wiki/Project_stakeholder "Project stakeholder") reap the benefits. However, authoring and creation of hypermedia is not trivial. Unlike in traditional authoring for hypermedia and the web, a linear storyline is not enough. Instead, various alternatives have to be created for the given material. For example, if a course should be delivered both to [visual](https://en.wikipedia.org/wiki/Visual "Visual") and [verbal](https://en.wikipedia.org/wiki/Language "Language") learners, there should be created at least two perfectly equivalent versions of the material in visual and in verbal form, respectively. Moreover, an adaptation strategy should be created that states that the visual content should be delivered to visual learners, whereas the verbal content should be delivered to the verbal learners. Thus, authors should not only be able to create different versions of their content, but be able to specify (and in some cases, design from scratch) [adaptation strategies](https://en.wikipedia.org/w/index.php?title=Adaptation_strategies&action=edit&redlink=1 "Adaptation strategies (page does not exist)") of delivery of contents. Issues with which authoring of adaptive hypermedia is confronted are:

- creation of exchange language for the content (some early examples are [the CAM language](http://journals.tdl.org/jodi/article/view/231/184)),
- creation of exchange language for adaptation (with [the LAG language](http://journals.tdl.org/jodi/article/view/231/184) and the [LAG-XLS language](https://archive.today/20121217161305/https://commerce.metapress.com/content/d7821442r8551453/resource-secured/?target=fulltext.pdf&sid=zqkup055h2ihdh45prjrnqm0&sh=www.springerlink.com) as examples),
- creation of a framework for adaptation (see, e.g., the [LAG framework](https://web.archive.org/web/20160304193644/http://www.dcs.warwick.ac.uk/~acristea/HTML/Minerva/papers/UM03-cristea-calvi-accepted.pdf)),
- standardization of adaptation processes.

There already exist some approaches to help authors to build adaptive-hypermedia-based systems. However, there is a strong need for high-level approaches, formalisms and tools that support and facilitate the description of [reusable](https://en.wikipedia.org/wiki/Reusability "Reusability") adaptive [hypermedia](https://en.wikipedia.org/wiki/Hypermedia "Hypermedia") and websites. Such models started appearing (see, e.g., the [AHAM model](https://en.wikipedia.org/w/index.php?title=AHAM_model&action=edit&redlink=1 "AHAM model (page does not exist)") of adaptive hypermedia, or the [LAOS framework](http://www2003.org/cdrom/papers/alternate/P301/p301-cristea.pdf) for authoring of adaptive hypermedia). Moreover, recently have we noticed a shift in interest, as it became clearer that the implementation-oriented approach would forever keep adaptive hypermedia away from the 'layman' [author](https://en.wikipedia.org/wiki/Author "Author"). The creator of adaptive hypermedia cannot be expected to know all facets of the process as described above. Still, he/she can be reasonably trusted to be an [expert](https://en.wikipedia.org/wiki/Expert "Expert") in one of these facets. For instance, it is reasonable to expect that there are content experts (such as, e.g., experts in chemistry, for instance). It is reasonable to expect, for adaptive educational hypermedia that there are experts in [pedagogy](https://en.wikipedia.org/wiki/Pedagogy "Pedagogy"), who are able to add pedagogical [metadata](https://en.wikipedia.org/wiki/Metadata "Metadata") to the content created by content experts. Finally, it is reasonable to expect that adaptation experts will be the one creating the implementation of adaptation strategies, and descriptions (metadata) of such nature that they can be understood and applied by laymen authors. This type of division of work determines the different authoring personas that should be expected to collaborate in the creation process of adaptive hypermedia. Moreover, the contributions of these various personas correspond to the different modules that are to be expected in adaptive hypermedia systems.

- MOT (My Online Teacher) 
- TANGOW 

## History

By the early 1990s, the two main parent areas – [hypertext](https://en.wikipedia.org/wiki/Hypertext "Hypertext") and [user modeling](https://en.wikipedia.org/wiki/User_modeling "User modeling") – had achieved a level of maturity that allowed for the research in these areas to be explored together. Many researchers had recognized the problems of static hypertext in different application areas, and explored various ways to adapt the output and behavior of hypertext systems to suit the needs of individual users. Several early papers on adaptive hypermedia were published in the *User Modeling and User-Adapted Interaction* (UMUAI) journal; the first workshop on adaptive hypermedia was held during a user modeling conference; and a special issue of UMUAI on adaptive hypermedia was published in 1996. Several innovative adaptive hypermedia techniques had been developed, and several research-level adaptive hypermedia systems had been built and evaluated.

After 1996, adaptive hypermedia grew rapidly. Research teams commenced projects in adaptive hypermedia, and many students selected the subject area for their PhD theses. A book on adaptive hypermedia, and a special issue of the New Review of Hypermedia and Multimedia (1998) were published. Two main factors accounted for this growth. Due a diverse audience, the [internet](https://en.wikipedia.org/wiki/World_Wide_Web "World Wide Web") boosted research into adaptivity. Almost all the papers published before 1996 describe classic pre-Web hypertext and hypermedia; the majority of papers published since 1996 are devoted to Web-based adaptive hypermedia systems. The second factor was the accumulation and consolidation of research experience in the field. Early papers provided few references to similar work in adaptive hypermedia, and described original laboratory systems developed to demonstrate and explore innovative ideas. After 1996, papers cite earlier work, and usually suggest either real world systems, or research systems developed for real world settings by elaborating or an extending techniques suggested earlier. This is indicative of the relative maturity of adaptive hypermedia as a research direction.

