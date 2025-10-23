# Introduction 
The notion of good data stewardship (i.e., maximizing the opportunities for the efficient discovery and reuse of research outputs) has been around for decades and many implementation choices have already been made by pioneering communities to extend stewardship with the notion of machine-actionability. The FAIR principles can be seen as a consolidation of these earlier efforts and emerged from a multi-stakeholder vision of an infrastructure supporting machine-actionable data reuse, i.e., reuse of data that can be processed by computers, which was later coined the “Internet of FAIR Data and Services”. 

The FAIR principles are intended as a guide to enable digital resources to become more Findable, Accessible, Interoperable and Reusable for machines and thus also for humans. These four foundational principles are more explicitly and measurably described by 15 FAIR guiding principles. Any interpretation or implementation of the FAIR principles may in essence be chosen as long as they lead to machine actionable results. This purposely means that individual stakeholder communities can define their own solutions and that these can be adapted over time as technologies evolve. While this freedom of choice may have contributed to the rapid and widespread adoption of the FAIR principles by stakeholders encompassing scientists, publishers, funding agencies and policy makers (for an overview see Budroni et al. it has also brought the inherent risk of incompatible solutions between stakeholder communities.

To reach the goal of an Internet of FAIR Data and Services, a global convergence towards accessible, robust, widespread and consistent FAIR implementations is required. The first step is to share a common, high-level interpretation of the FAIR principles. **Mons et a**l  discussed early emerging misinterpretations of the FAIR foundational principles and clarified their original intent and interpretation. They emphasize that “FAIR is not a standard … FAIR is not equal to RDF, Linked Data, or the Semantic Web … FAIR is not just about humans being able to find, access, reformat and finally reuse data … FAIR is not equal to Open … FAIR is not a Life Science hobby”.

Moreover, a desire to expand the purposely limited scope of the principles has led to suggestions to extend the FAIR acronym with additional letters [6], often unrelated to the specific objective of facilitating data reuse by machines. Thus, a more detailed and common understanding of the scope, aim and representative implementation choices for each FAIR principle would be helpful to improve their stepwise application by diverse stakeholders, and stimulate FAIR adoption in more geographies and new scientific communities

There are several alternative routes towards the implementation of the FAIR principles, some specialized for different types of digital resources. Communities have already published documents that can guide implementation choices. Examples are: “the FAIR metrics”,  the follow-up Maturity Indicators, “Top 10 FAIR Data & Software Things”, the RDA FAIR Data Maturity Model, the EC report on “turning FAIR into reality”, and the “FAIR principles explained” described on the GOFAIR website.

Some common community considerations can already be identified:
- existing technologies should be used where possible
- The process of making resources FAIR (“FAIRification”) can typically be broken down into steps, allowing the different facets of FAIRness to be prioritized depending on the resource under consideration
- the cost-benefit to the implementer and their community stakeholders
- different types of stakeholders adopt complementary roles with respect to implementing FAIR principles where the implementation decisions for certain kinds of stakeholders can be shared and reused across domains or communities.
	- (e.g. a domain expert, an information scientist, a system engineer, a data archivist, a data mining agent)
To facilitate the harmonization of FAIR implementation choices between and within communities, we provide, here, a directed set of FAIR implementation considerations, which include: a discussion and nontechnical interpretation of the relevant principle being considered; some examples of existing solutions; and discussions of the challenges that must be considered when approaching the design of a novel solution. Guided by these implementation considerations, a stakeholder community may choose to reuse a solution from among existing implementations, or if none of these appear suitable, will have a clear roadmap describing the challenge in creating a de novo solution for the identified gap. A platform where stakeholder communities can declare their FAIR choices and challenges – the FAIR Convergence Matrix – is described in a separate paper

Although maximizing the freedom to operate is a key feature of the “hourglass” approach that drove the rapid development of the Internet, and allows a multitude of FAIR solutions to flourish, a common understanding around the original intentions of the guiding principles is crucial to avoid divergence into non-interoperability once again. The purpose of this article, therefore, is to express the opinions of the original creators of the principles, supported by discussions of the experiences of pioneering FAIR implementers
# Why make your research data FAIR? 
Making your research data FAIR could help to maximize your research impact and help peers and your ‘future self’ understand your research projects and data. But don’t just take our word for it. Hear more from two of the authors of the FAIR principles on the advantages of making research data FAIR here.
Reusing existing data sets for new research purposes is becoming more common across all research disciplines.
Research funders and publishers are asking researchers to make data sets produced in their projects available to others. And research institutes are promoting measures to secure the transparency and accessibility of locally produced data sets. To facilitate this, datasets need to be Findable, Accessible, Interoperable and Reusable.
## Top level page: What is FAIR?
The FAIR universe is populated by concepts and acronyms that you may not be familiar with. You will learn about all of them, as you make your way through this website. On this page, you can read more about three fundamental concepts: the FAIR principles, FAIR data, and FAIRification practices.
We presuppose that you have some knowledge of general research data management practices. If you are completely new to the field of research data management, we recommend that you start by taking our eLearning course on research data management.
### The FAIR principles
The FAIR principles, first published in 2016 (link to M.D. Wilkinson, M. Dumontier, I.J. Aalbersberg, G. Appleton, M. Axton, A. Baak, … & B. Mons. The FAIR guiding principles for scientific data management and stewardship. Scientific Data 3(2016), Article No.160018), contain guidelines for good data management practice that aim at making data FAIR: Findable, Accessible, Interoperable, and Reusable.
"Data" refers in this context to all kinds of digital objects that are produced in research: research data in the strictest sense, code, software, presentations, etc. However, for the sake of simplicity, we use "data" and not "digital research objects" on this website.
Each letter in FAIR refers to a list of principles with a total of 15 principles altogether. A recent paper on the FAIR principles introduces the useful distinction between the four foundational principles that aim at findability, accessibility, interoperability, and reusability, and the 15 guiding principles that more explicitly and measurably describe how FAIRness of data can be achieved through technical implementation
Although the FAIR principles originate from the life sciences, they can be applied within all research disciplines. Since their publication, the European Union, national funders, and universities have declared their support for and approval of the FAIR principles. This spans from defining policies for data handling to the creation of data management tools and infrastructures. Some FAIR projects and implementations stick closely to the original definitions, while others are inspired by the spirit of the FAIR principles
#### In order to properly understand FAIR, you should keep in mind 5 things: 
1. Both humans and machines are intended as digesters of data. This will lead to the creation of an ecosystem that is fast to respond to change and automatically adapts to new findings or changes: the Internet of FAIR Data and Services
2. The FAIR principles apply to both data and metadata, i.e. descriptions of or records about data. This is why the term “(meta)data” is stated in the principles.
3. The principles are not necessarily about open data. You can work in a FAIR manner with data that is not intended for public availability.
4. The FAIR principles must not be mistaken for rules or standards that you can use to evaluate tools, data, policies, etc. This would soon make the principles out-of-date and inapplicable across research disciplines. 
5. Adopting the FAIR principles will often be a gradual adaptation of work routines – but it could also be a huge leap, where you replace one type of infrastructure with another. It will be up to the different research areas and research communities to make the FAIR principles work in their respective contexts
### [[FAIRUST Data]]
**Findable** means that the data can be discovered by both humans and machines, for instance by exposing meaningful machine-actionable metadata and keywords to search engines and research data catalogues. The data are referenced with unique and persistent identifiers, e.g. DOIs or handles, and the metadata include the identifier of the data they describe.
**Accessible** means that the data are archived in long-term storage and can be made available using standard technical procedures. This does not mean that the data have to be openly available for everyone, but information on how the data could be retrieved (or not) hasto be available. For example, data can be marked “Access only with explicit permission from the author” and include the author’s contact details. Ideally, though, the information about data accessibility can also be read by machines, e.g. by way of machine-readable standard licenses.
**Interoperable** means that the data can be exchanged and used across different applications and systems — also in the future, for example, by using open file formats. It also means that the data can be integrated with other data from the same research field or data from other research fields. This is made possible by using metadata standards, standard ontologies, and controlled vocabularies as well as meaningful links between the data and related digital research objects.
**Reusable** means that the data are well documented and curated and provide rich information about the context of data creation. The data should conform to community standards and include clear terms and conditions on how the data may be accessed and reused, preferably by applying machine-readable standard licences. This allows others either to assess and validate the results of the original study, thus ensuring data reproducibility, or to design new projects based on the original results, in other words data reuse in the stricter sense. Reusable data encourage collaboration and avoid duplication of effort
### FAIRification practices
How you apply the FAIR principles, depends on your specific discipline and your way of doing research. But there are different activities you must consider within your research workflows, if you want to make your data FAIR. For instance: documenting your data, choosing appropriate file formats, adding metadata, giving access to the data, licensing the data or adding a persistent identifier. 
#### How can you make your data FAIR
1. documentation 
2. file formats
3. metadata
4. access to data
5. persistent identifiers 
6. data licenses 
#### FAIR best practice examples
You make your research data accessible by: 
- Attaching a data license or clear data accessibility statement in your openly available administrative metadata 
- Ensuring your data are archived in long-term storage and retrievable by their persistent identifier using a standard protocol 
- Giving access to the metadata, even if the data are closed
You make your research data interoperable by:
- Including sufficient and standardized structural metadata in accordance with your research community’s standard controlled vocabulary or ontology
- Including use of common standards, terminologies, vocabularies, ontologies and taxonomies for the data
- Preferring open, long-term viable file formats for your data and metadata
You make your research data reusable by:
- Attaching all the relevant contextual information required for re-use in either the documentation or metadata attached to your data
- Including sufficient and standardized structural metadata in accordance with your research community’s standard controlled vocabulary or ontology
- Preferring open, long-term viable file formats for your data and metadata
- Applying a machine-readable data license


## [[FAIR Documentation]]
## File Formats 
File formats determine how data can be used. It is important to decide what file formats to use for data collection, data processing, data archiving, and long-term preservation.
### What are file formats 
Files are saved in different formats. File formats are standard ways to store data on a computer and define how bits are used to encode information in a digital storage medium. Files are usually named like this: [prefix].[suffix] or filename.type. The prefix is a name which is used to identify the file, and the suffix indicates the file type. In this way files of the type .txt are text encoded files and usually contain text and/or numbers. Images are often saved in .jpg or .bmp while audio can be saved in the .mp3 or .wav file format.

Some file formats are proprietary – like .nef or .wma which are owned by Nikon and Microsoft. Other file formats like .txt or .csv are non-proprietary and can be used with a variety of software. Different file formats have different characteristics and properties and thus determine how data can be used. The purpose of a file should help determine which file format to choose. Therefore, you may have to keep some data files in multiple formats. It is important to plan what file formats to use for each purpose: data collection/ processing/analysis, reuse, and preservation

When it is necessary to save files in a proprietary format, consider including a readme.txt file in your directory that documents the name and version of the software used to generate the file, as well as the company that made the software. This could help you down the road, if you need to figure out how to open these files again

### While you work 

The researchers in the ISSP use both STATA and SPSS in order to accommodate that data users can use their preferred statistical software. Both are proprietary formats for statistical data, but the formats can retain a lot of the information in the data like the values and variable names as well as variable and value descriptions (labels) that cannot be stored in CSV. Conversions between the two formats are easily done as each software solution supports saving in other formats. To make sure that no information is lost in file conversions, a copy of the original dataset is kept unaltered.

### Publish and Preserve 
You have to consider whether the file formats used for data collection, processing, and analysis are also appropriate formats for long-term preservation. Choosing the right file format for publishing and preserving research data determines how or even if you or others can access and use the data later.
#### Examples of preferred FAIR file formats for preservation

| Containers      | TAR, GZIP, ZIP                           |
| --------------- | ---------------------------------------- |
| Databases       | XML, CSV, JSON                           |
| Geospatial      | SHP, DBF, GeoTIFF, NetCDF                |
| Video           | MPEG, AVI, MXF, MKV                      |
| Sounds          | WAVE, AIFF, MP3, MXF, FLAC               |
| Statistics <br> | DTA, POR, SAS, SAV                       |
| Images          | TIFF, JPEG 2000, PDF, PNG, GIF, BMP, SVG |
| Tabular Data    | CSV, TXT                                 |
| Text            | XML, PDF/A, HTML, JSON, TXT, RTF         |
| Web             | WARC                                     |
#### What are other considerations 
When choosing file formats to publish or preserve your data, try to keep the following considerations in mind:
**Choose formats common to your field**
To ensure the interoperability and reusability of your data, it is vital to preserve the data in a format common to your field.

For example, as long as Carsten Brink is working with the images and the patients, he would not convert his DICOM images to TIFF, because the TIFF-format cannot contain all the metadata that are captured in the DICOM format (patient data, place of tumor, duration and dose of radiation etc.). But if the purpose later on is to preserve the images themselves, then TIFF would be an appropriate format. Any relevant metadata could be saved in another file in TXT format.

##### How long do you intend to preserve your data? 
Time is a critical factor for the right choice of preservation formats.

The longer the time span for the future use of the data, the more you will have to use open, standardized and well documented file formats in order to avoid obsolescence

At the same time, you must consider the storage media for your data – where to store your data? For more information, have a look at the Access to data page
##### File conversion can lead to data loss 
To avoid loss of information in file conversion, it is important to use a common, multi-platform file format that follows specific standards, as you work on the data across different platforms and software solutions.

If conversion to an open data format could result in some data loss from your files, you might consider saving the data in both the proprietary format and an open format. Also there might be errors in the conversion software that is used, so it is a good idea to check the data before and after.

For example: If you need to keep the history of changes in a document written in MS Word, you should not keep the file in pdf only, but in both formats, to ensure optimal usability and preservation of data and metadata. This might not always be possible due to storage space constraints but is worth considering.

##### Check data repository requirements 
Many journals, archives and data repositories require that data are uploaded in certain file formats

This is important to plan for already in the beginning of your project, so you can choose the best file format for collection, processing and ultimately sharing with conversion between the different phases of your research in mind
## Metadata
Metadata are data about data. Research data need metadata to become findable, accessible, interoperable and reusable - by humans and machines.
### What is metadata 
Metadata are data about data. They play an important role in making your data FAIR. Metadata have to be added continuously to your research data, not just at the beginning or at the end of a project. They can be added manually or automatically, and preferably according to a disciplinary standard. From a FAIR perspective, metadata are more important than your data, because metadata would always be openly available and they link research data and publications in the Internet of FAIR Data and Services. The distinction between data and metadata is not ontological, but it is grounded in use. What is “data” and what is “metadata” is thereby a matter of perspective: Some researchers’ metadata can be other researchers’ data.
#### 3 types of meta data 
1. Administrative metadata
		data about a project or resource that are relevant for managing it; for example, project/ resource owner, principal investigator, project collaborators, funder, project period, etc. They are usually already assigned to the data, before you collect or create them.
2. descriptive or citation metadata 
		data about a dataset or resource that allow people to discover and identify it; for example, author(s), title, abstract, keywords, persistent identifier, related publications, etc.
3. structural metadata 
		about how a dataset or resource came about, but also how it is internally structured. Structural metadata describe, for example, the unit of analysis, collection method, sampling procedure, sample size, categories, variables, etc. Structural metadata have to be gathered by the researchers according to best practice in their research community and will be published together with the data. Descriptive and structural metadata should be added continuously throughout the project.
##### Since your data may change while you conduct your research, you should make sure that your metadata change accordingly.

### Metadata Standards and ontologies 
The quality of your metadata has a huge impact on the reusability of your research data. It is best practice to use a metadata standard and/or an ontology commonly used in your field to describe your data. Some data repositories can help you in choosing an appropriate metadata standard for your data.
#### What is a metadata standard 
A metadata standard is a subject-specific guide to your metadata. Metadata elements are grouped into sets designed for a specific purpose and given a standard name and definition. Rules on what content must be included, what syntax must be used, or a controlled vocabulary can also be included in a metadata standard.
#### What is an ontology
An ontology (or controlled vocabulary) provides a standard definition of key concepts in a subject area with a focus on how those concepts are related to one another. It can be used to define the structure of your data. 

An ontology is considered to be a subset of a taxonomy, which is a hierarchically structured conceptual representation of a subject area. Ontologies and taxonomies are closely related, but distinct concepts.

#### What can you do if there is no standard in your field 
Metadata standards often start as schemas developed by a particular research group or community to enable the best possible description of their data.

#### What if the existing metadata standards in your field are incomplete?
What if the existing metadata standards in your field are incomplete? The available metadata subsets in TEI (Text Encoding Initiative) are not specifically suited for the annotation of parliamentary speeches and debates. Therefore, the research community of the Language Technology Group is currently collaborating to define a relevant metadata scheme for this purpose. Read more about this in the workshop description and proceedings of ParlaCLARIN-II. 

### Taxonomy 

A data taxonomy is a hierarchical system for classifying and organizing data into categories and subcategories based on shared characteristics and relationships, making data easier to find, understand, and use across an organization. It acts as a controlled vocabulary, providing structure and consistency to data management, which enables better data governance, analysis, and decision-making by breaking down data silos and improving data discovery

Since there was no metadata standard specific to his field, Nikola Vasiljević joined a team of researchers working on a taxonomy to describe collected data. The team based their taxonomy on the Dublin Core Metadata card, which contains 15 descriptive metadata entries. To make the taxonomy more useful within their field, they added 5 additional metadata entries: External Conditions, Activities, Instruments, Models, Materials

A taxonomy was developed for several of the entries in the metadata card. When you catalogue your data with such metadata cards, search engines can explore the metadata cards and end-users can easily find the right dataset for their own research.

### Publish and Preserve
Publishing your research data and metadata online provides you with an extra location for people to find your work. Even though your publications contain your results, your data may still not be findable. Metadata are machine readable, and when they have a persistent identifier, search engines can easily find them.

To be FAIR, your data (or metadata) must have a findable persistent identifier. The persistent identifier is typically assigned when a digital resource is placed in a data repository.

#### What if you work with sensitive data?
Many researchers cannot openly publish their data. However, you can always publish rich metadata about your data.

For sensitive data, publishing metadata provides you with a platform where you can make clear under which conditions the data can be accessed and how they may be reused.
## Access to Data
Conducting research is often a team effort. Even before collecting the data, it is important to consider who will get access to the data, under which conditions and what permissions they will have.

### Plan for access 
As you create and collect data it is important to have a plan for the access to your data in a data management plan. In the plan, reflect on the following questions:
- who should have access to the data and under which conditions 
- How are the data backed up?
- How is the above documented?
- Intellectual Property Rights (IPR) agreements may restrict access to data sets both during the collection and after finalizing the project.
### What about sensitive data 
Sensitive data can be FAIR without being open. The FAIRness is made by a clear description on how access to the data can be granted e.g. for research purposes.

A lot of research is based on sensitive personal data, data protected by IPR (Intellectual Property Rights) agreements or confidential data. This means that access to the data must be managed and restricted.

#### What other options do you have for providing access to sensitive personal data?
To share sensitive data with others, you can anonymize (change to impersonal ID's) or de-identify (remove ID's) them - but there are many problems with this approach
##### The first problem
is that anonymization is not trivial and may even be impossible. For instance, if you have a CT scan of the head of a patient others might be able to make a surface reconstruction that in some cases could break the intended anonymization if the patient has some specific anomalies. If you can’t anonymize, you can’t directly give access.
##### The second problem
is that it is not possible to add new data to anonymized patient information. So, if, 5 years later, you observe that the patient is still alive, you cannot just add that piece of information. This limits the reusability of the data.
##### The classic way
to work with data from different institutes is to pool them on a central server, which hosts the analysis tools, creating a data lake. You collect the data, you perform your analysis and then you effectively throw them away. The data are often not reusable and therefore not perfectly FAIR.
#### Distributed learning
Within Carsten Brink’s research area, researchers from all over the world work together. To predict what outcome a treatment for their patients will have, they develop a model. They can base this model on the patient data at their own hospital, but in some cases, the model will require more data than their own hospital can provide. In this case, they can send their model from institute to institute, collecting results along the way. This is called Distributed Learning.

Depending on the size of the project, distributed learning can be a FAIR practice to handle sensitive personal data responsibly, but it does require a substantial overhead of mapping data to a standard format.
#### Where else can you preserve your data 
Many institutions and public organization's have established data repositories, where scientists can upload and share their data. Projects funded by the European Union are expected to make generated data or their metadata available to the public - for example through data repositories.

To search for a suitable repository for your research data you can visit [re3data.org](), which is a global registry of research data repositories from different academic disciplines, FAIRsharing, which allows you to discover databases grouped by domain, species or organization, or check the links page to find more resources on data repositories. 
## Persistent Identifiers 
To make your data easy to find and accessible, you must provide your data and metadata with a persistent identifier. A persistent identifier is a long-lasting reference to a digital resource and provides the information required to reliably identify, verify and locate your research data.
### What is a persistent identifier 
A persistent identifier (PID) is a long-lasting reference to a digital resource and provides the information required to reliably identify, verify and locate your research data eliminating many misunderstandings. A PID may also be connected to a set of metadata which describes a digital resource.

Notable persistent identifiers are the Digital Object Identifier (DOI) and the Handle System which can both be assigned to data to identify them uniquely. The DOI system uses the Handle System, which is the best infrastructure component available today for managing digital objects. While DOIs are mainly assigned to resources ready for public dissemination, Handles are in general used to persistently identify other categories of digital resources (e.g. those created in the labs) to make them referable by software, workflows etc
### How to get a PID
To make your data accessible and easy to find, you must provide your data and/or your metadata with a PID. This can be done by making a record in a trusted or recommended repository.

Before assigning a PID, it is important to consider who has the responsibility and the rights for registering the PID and, later on, making updates if needed.

**No PID? Not FAIR**
If your data and/or metadata are only stored internally at your institution or at another repository that does not issue a PID, they will not be FAIR
### Which data need a PID
 - ensure retractability 
	 - To ensure reproducibility of your research data, assign persistent identifiers to your processed data.
 - Ensure reuse
	 - To ensure your research data can be reused you can assign persistent identifiers to the raw data, and not as the last task before they get archived but immediately after data collection!
## Data Licenses  
A data license is a legal arrangement between the creator of the data and the end-user specifying what users can do with the data.
### What are data licenses
It’s great to publish - both data and articles but releasing data without making the terms of use clear can be confusing and counterproductive, as it may deter potential users from using and citing the data. One of the most effective ways to communicate permissions to potential users of data are the data licenses.

A data license is a legal arrangement between the creator of the data and the end-user, or the place the data will be deposited, specifying what users can do with the data. Here are some examples of data licences at three different repositories.

Many institutions and public organisations have established repositories, where scientists can upload and share their data. Projects funded by the European Union are expected to make generated data or their metadata available to the public and attach a data licence - for example through data repositories.

### The CC licenses
The most commonly and widely used data licences is the suite of Creative Commons (CC) copyright licences which clearly describe how data can and cannot be reused. The CC licences are irrevocable. This means that once you receive material under a CC licence, you will always have the right to use it under those licence terms, even if the licensor changes his or her mind and stops distributing under the CC licence terms. Of course, you may choose to respect the licensor’s wishes and stop using the work, but once a data set has been issued a CC licence, it cannot be revoked afterwards. A scientific data set, which other researchers may build upon or which is published together with a scientific article, is usually published under the CC-BY license.
# [[Test your FAIR-ability]]
This quiz will take you through some example research situations, where the best practices for FAIR may clash with what is practical or possible in reality.

For each question, you will be asked to decide whether the answers are “FAIR” or “NOT FAIR”.

As you have learned throughout the course, FAIR is not black or white, but a continuum, in which your research data can become more or less FAIR based on your decisions.

To test your understanding of the principles, however, you will be asked to decide whether an answer is “FAIR” or NOT FAIR”.
