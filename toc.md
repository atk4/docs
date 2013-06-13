<!-- 
Agile Toolkit Documentation: Table of Contents
-->

<!-- 

== MOVE TO WEBSITE: EVALUATING AGILE TOOLKIT==
= Needs restructuring =

* What Are The Benefits Of Agile Toolkit?
	+ Why Learn A New Framework?
		- Agility Is Hard
		- Rich Web Applications Are Hard
		- Reusability Is Hard
		- The Need For New Thinking
	+ A Better Way To Build Agile Applications
	+ A Better Way To Build Rich Web Applications
	+ A Better Way To Develop Reusable Code
	+ Other Key Benefits Of Agile Toolkit
		- Elegant & Powerful ORM
		- Security By Design
      	- Collaboration By Design 
      	- Testability By Design
      	- Backward Compatibility By Design
      	- Free & Commercial Addons
	
	+ Differences From Conventional Frameworks
    	- Agile Toolkit vs PHP Server-Side Frameworks
     	- Agile Toolkit vs JavaScript Toolkits
     	- Agile Tookit vs Other Integrated Toolkits
			* Extensible Objects vs Code Generation

    	
* What Does The Code Look Like?
    + Hello World
    + Example Gallery ->>
    + Open-Source Projects ->> 
      	
* Is Agile Toolkit Right For You?
    + What Types of Project Will Benefit?
		- Fast Evolving Requirements
		- Rich Data-Management Interfaces
		- Complex Data Interractions
		- Adaptable Application Platforms
	+ What Developer Skills Are Required?
		- Technical Skills
		- Developer Mindset
    + How Can I Learn Agile Toolkit?
		- User-friendly Documentation
		- Hands-on Tutorials
		- Practical Cookbook
		- Developer API
			* Reading The Code
		- Helpful Community
		- What Commercial Training is Available?
    + What Are The Other Issues With Adoption?
		((Please draw up some notes))
	+ Is The Licence Suitable?
		- Open Source Projects
		- Schools & Universities
		- Internal Commercial Use
		- Closed Source Web Apps
		- Large Web Apps
		- OEM Distribution
	+ What Is The Future Of Agile Toolkit?
		((Please draw up some notes))
-->

*  Installation
    + Requirements										
    + Directory Structure
    	- Core Directories
      	- Addon Directories
      	- Application Directories
	+ Installing With Composer 							
    + Installing Manually 								
	+ Starting A New Project 							
        - Using The Agile Toolkit Installer
        - Setting Up The Admin UI
    + Integrating With Existing Projects 				
    	- Overview
		- Integrating With CodeIgniter
    	- Integrating With Yii
    	- Integrating With Symfony
    	- Integrating With Joomla
    	- Integrating With Wordpress
    + Securing Your Installation 						
	+ Upgrading Your Installation 						
	+ Advanced Configuration 							
    + Troubleshooting 									

* Overview

    + Why Use Agile Toolkit?
    + What Are The Key Design Principles?
    + A Fresh Approach To MVC
    + Anatomy Of An Application
    + How To Learn Agile Toolkit

* Tutorials
	+ Hello World ->>
	+ DVD Rental (Beginner) ->>
	+ School Enrollment (Intermediate) ->>
	+ See More Tutorials ->>
	+ How to Contribute Tutorials

* Reference

    + Request Flow Chart
    + Agile Toolkit Cheat Sheet
    + Coding Standards

* Working With The Core

    + Working With Application APIs
    	- API Overview
			* What Is An Agile Toolkit API?
			* Selecting The Right API
      		* Security Considerations
    	- ApiCLI
			* Features
			* Command-Line Applications
			* Cron Job Applications
      		* Queuing Applications
    	- ApiWeb
			* Features
			* Integrating With 3rd Party Frameworks
    	- ApiFrontend
			* Features
			* Using With Web Applications
    	- ApiInstaller
      		* The Agile Toolkit Installer
    	- ApiJSON
			* REST APIs
		- Other APIs
    	- Running Multiple APIs
			* Overview
			* Use Cases
    	- Extending API Classes
			* Overview
			* Use Cases

    + Working With Framework Objects
        - The Class Hierarchy
        - Application Structure
            *APIs
            * The Runtime Object Tree
        - Adding Objects
        - Object Naming
        - Initializing Objects
        - Cloning Objects
        - Addons & Namespaces

	+ Configuration
    	- Working With Config Files
    	- Default Settings
    	- Adding Your Own Settings
    	- Locale Settings
    	- Addon Settings

	+ Class Loading								
		- The PathFinder Class					
		- Loading Addons					
	 	- Using Namspaces			
	   	- Using 3rd Party PHP Libraries
			* Manual Integration
			* Integration Using Composer
		- Advanced Loading						

	+ Request Routing
		- Overview
    	- Static pages
    	- Dynamic pages
    	- URL Generation
    	- Advanced Routing

	+ Asset Management
    	- CSS
    	- JavaScript
    	- Images & Media
    	- Advanced Asset Management
    		* Uploaded files ((Link to filestore docs)
    		* Loading Assets From Other Domains

  	+ Event Hooks
    	- Overview
    	- Adding Hooks
		- Passing Arguments
		- Setting Priorities
    	- Breaking Hook Chains
		- Best Practices
			* Benefits And Risks
			* Use Cases
			* Testability
			* Reusability
    	- List Of System Hooks

	+ Authentication & Authorization
    	- Terminology
    	- The BasicAuth Class
    	- OAuth Open Authentication
    	- Login Forms
    	- Permissions

  	+ Error Handling
    	- Error Handling Overview
		- Framework Errors
    	- Application Errors
    	- 404 Handling
		- Best Practices

	+ Testing
  		- Unit Testing
    		* Built-in Unit Testing
    		* Using 3rd Party Tools
    	- Functional Testing
    		* Selenium
    		* Behat

* Working With Models

	+ DSQL Query Builder
    	- Overview
      	- Basic Usage
      	- Defining Queries
		- Running Queries
		- Advanced Usage
      	- Extending Queries
	+ Relational Models
  		- Overview
      		* Design Goals
			* Comparison With Other ORMs
      		* Features
			* Performance     		
    	- Concepts
      		* Data-set
      		* Loaded Record
      		* Field Meta-information
      	- Validation
      	- Caching
      	- Hooks
      	- Actions
      	- Binding Models To Views
      	- Actual Fields
      	- Aliases
    	- Defining Models
      		* Fields 
      		* Expressions
      		* Relations
      		* Joins
      		* Using DSQL
    	- Data Filtering
      		* Basic Use
      		* Use with Expressions
      		* Use with Subselects
      		* Use with other Models
    		* Order
    		* Limit
		- Data Summarizing
      		* Count and Sum
      		* Grouping
      		* Options
    	- Using Models
	      	* Loading and Saving
	      	* Iterating Result Sets
	      	* Traversing Result Sets
	    - Examples
	      	* Timestamps
	      	* Cached Fields
	    - Flexible Data Components
	      	* Extending Models
	      	* Overriding Models
	    - Best Practices
	      	* Defining Model Methods
	      	* Extending Models With Controllers
	      	* Overriding Default Methods
	    - Behaviour Hooks
	      	* Loading & Saving
	      	* Query Building
	      	* Validation
	    - Extensions
	      	* Database Builder - Create Schema From Model
	      	* Model Builder - Created Models From Schema     	
	    - Database Migration
			* Keeping The DB In Sync With The Models
	      	* Visual Migration Tools
		- Using With SQLite 
	+ NoSQL Models
   		- Overview
      		* Goals
			* Features
      		* When Not To Use
    	- Defining ((What do you have in mind here??))
    	- Array Controller
      	- Session Controller
      	- JSON API & CURL
      	- Memcache Controller
      	- MongoDB Controller
      	- Redis Controller
    	- Creating A New NoSQL Controller
    	- Using A NoSQL Controller As A Cache
    		* Using A Single Cache
			* Using Multiple Caches 
    	- Database-independent Result Set Traversal

* Working With Views       

  	+ Pages
    	- Overview
      		* Goals
      		* Features
    	- Best Practice
    	- Design Workflow
    	- Reusable Sub-pages
    	- Custom Page Patterns ((What goes here??))
	+ Templates
		- Overview
			* Goals
			* Features
    	- Creating Templates
    	-  Using Templates
  	+ The CSS Framework
    	- Overview
			* Goals
			* Features
		- Customizing The Default Skin
    	- Creating New View Templates
 	+ Views
    	- Overview
			* Goals
			* Features
		- Initializing Views
    	- Binding Data
    	- Updating Views
		- Developing New Views
	+ Forms
		- Overview
			* Goals
			* Features
    	- Creating Forms
    	- Handling Submissions
    	- Validating Inputs
		- Displaying Error Messages

* Working With jQuery

	+ The JavaScript API
    	- Overview of JavaScript and AJAX
			* Goals
			* Features
    	- Using The Bundled Widgets
			* Overview
			* List
			* Grid
			* CRUD
			* etc...
    	- Creating Your Own Widgets
    	- Using 3rd Party JQuery Plugins
  	+ Working With JavaScript Events
    	- Overview
			* Goals
			* Features
    	- Binding JavaScript Chains
    	- Enclosing Chains
    	- Using Multiple Chains
    	- Calling Your Own JavaScript
    	- Customizing Selectors And Triggers

* Working With Addons
  
  	((RM: add-ons would be separate, but we need add-on development guide))
	((GC: Why woudn't we document the official addons here? What alternative are you suggesting?))
	+ Filestore
  	+ Google Maps
    + Localization
	+ Etc...
	+ How To Access Community Addons
 	+ How To Create Your Own Addons

* Developing With Agile Toolkit
	+ Structural Choices
    + Agile Development
    + Best Practices
    + Security
	+ Performance & Scaling
    	- Concepts & Strategy
    	- Performance In Agile Development
    	- Profiling & Benchmarking
    	- Automated Speed tests
    	- Output caching
    	- Input caching
    	- Clustering
    	- Cloud Services
    		* Amazon AWS
			((Others??))
	+ Deployment
 		
* Cookbook  
 	+ A useful example of a Cookbook entry ->>
	+ Another useful cookbook entry ->>
	+ A final useful cookbook entry -->>
	+ See more Cookbook Recipes ->>
	+ How to Contribute Recipes
