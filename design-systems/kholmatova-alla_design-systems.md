# Design Systems by Alla Kholmatova

"But by themselves, design patterns aren't enough. They need to live within a larger process, on that ensures these little interface modules feels unified, cohesive, connected. Part of a whole. In other words, they need a design system to thrive -- and that's where Alla's book comes in." - Ethan Marcotte

A design system should inspire teams to contribute to them.
A design system should get better, more cohesive and better functioning with time (not bloated and cumbersome).

## Glossary
---
- **Pattern**: Any repeating, reusable part of an interface (e.g. button) that can be applied and repurposed to solve a specific design problem, meet a user need, or evoke an emotion.
	- **Functional Pattern (or Module)**: Related to behaviors. The HTML.
		- e.g. buttons, headers, menus
	- **Perceptual Pattern (or Style)**: Related to brand and aesthetics. The CSS.
		- e.g. iconography, colors, typography
- **Pattern Language/Design Language**: A set of interconnected, shareable design patterns for the language of your product's interface. 
- **Design System**: many defintions. Here it's the set of connected patterns and shared practices, coherently organized to serve the purposes of a digital product.
- **Pattern Library**: A tool to capture, collect and share design patterns and guidelines for their usage.
- **Style Guide**: Focuses on styles, such as iconography, colors and typography. 

## References
---
This book includes insights from:
- Airbnb: Roy Stanfield (Principal Interaction Designer). They have something called the Design Language System (DLS
- Atlassian: Jürgen Spangl (Head of Design), James Bryant (Lead Designer), Kevin Coffey (Design Manager). They have the Atlassian Design Guidelines (ADG)
- Eurostar: Dan Jackson (Solutions Architect)
- Sipgate: Tobias Ritterbach (Experience Owner) and Mathias Wegener (Web Developer). They have the Sipgate Pattern Library which was deprecated in favor of a new pattern library due to having too many patterns.
- TED: Michael McWatters (UX Architect), Aaron Weyenberg (UX Lead), and Joe Bartlett (Front-End Developer). They don't see the need for a pattern library. Instead, they have documented patterns in a simple way (TED Swatch).

## Chapter 1: Design Systems
---
A design system is a set of interconnected patterns and shared practices coherently organized to achieve the purpose of digital products.
- Patterns: the repeating elements (e.g. user flows or buttons)
- Practices: how we choose to create, capture, share, and use those patterns

The purpose of a design system is to help achieve the purpose of a product.

### Design Patterns
The purpose of the product shapes the design patterns it adopts (quick scanning data vs thoughtful reading). The ethos (or brand) also forms perceptual patterns.

"Each pattern describes a problem that occurs over and over again in our environment, and then describes the core of the solution to that problem." - Christopher Alexander (author of A Pattern Language)

http://ui-patterns.com for a great source of common design patterns

If you need to get a group of people to follow a creative direction consistently, reliable and coherently, patterns need to be articulated and shared.

When the design language is shared knowledge, you can stop focusing on the patterns themselves and instead focus more on the user. Small design changes can be applied quickly to multiple products.

### Shared Language
A shared language is essential. Without a shared language, a group of people can't create effectively together. Engineers and designers should call things the same things.

https://www.howtomakesenseofanymess.com/ suggests that a shared language should be established before you think about interfaces.

### Pattern Libraries
A pattern library is one good practice in supporting a design system. It collects, stores and shares design patterns, along with the principles and guidelines for how to use them.

e.g. NASA's Graphics Standards Manual, Yahoo's pattern library, MailChimp's pattern library

On the web, pattern libraries can include logo treatments, corporate values, typography, color palettes, interface modules, guidance for use.

**Note:** This is not the system itself. It's just a tool that helps make a design system more effective. It has little impact on the actual user experience.

A pattern library is not effective if only a small number of people use it, or if there are too many disconnected patterns as a result of a lack of communication between teams.

It should still be flexible enough to allow creative experimentation. 

### Effective Design Systems
A design system can be considered to be effective when it combines cost-effectiveness in the design process, and efficiency and satisfaction of the user experience in relation to the product's purpose.


## Chapter 2: Design Principles
---
Solid principles are the foundation for any well-functioning system.

Teams choose how to achieve the product's purpose through design. The more aligned they are on their principles, the more cohesive they patterns they create.

Design principles are shared guidelines that capture the essence of what good design means for the team, and advice on how to achieve it. 

Establish a few grounding values and principles to make sure that the purpose of the product is manifested through design. Can be brand-focused, e.g. "Lucid/Animated/Unbreakable" (Pinterest), or process-focuesed, e.g. "Do less. Iterate. Then iterate again." (UK GDS)

Larger companies might have separate sets of principles for the user experience, the brand and the design system. Each team might also have their own team principles. Note: this can contribute to a design system's fragmentation.

### Effective Design Principles
1. They're authentic and genuine
	- Some things, like accessibilty, performance, and Dieter Rams' ten commandments of good design, are a given.
	- Should not be the sort that can be interpreted in many ways
	- e.g. TED's "By timeless, not cutting edge" means no one is going to introduce something trendy (e.g. parallax effects)
2. They're practical and actionable
	- Don't be vague. Say "Only one number 1 priority" vs "Make it clear" or "Every design element must have a purpose. If it doesn't, remove it." vs "Make it simple"
	- Should offer actionable advice
	- It helps to include real life examples
3. They have a point of view
	- Good design principles help to work out priority and balance, even if there are conflicting factors to consider. e.g. Saleforce's "Clarity. Efficiency. Consistency. Beauty." has a specific order of importance.
4. They're relatable and memorable
	- Should be recalled instantly
	- Refer to them in everyday conversations, presentations, etc..
	- Don't have too many (three to five)

### Defining Your Principles
Start with the purpose. Look at your company's values or product vision. 
Figure out what good design means for your product. Find examples. See if there's unity with other team members.
Focus the principles on the right audience. You're not writing them for a company brochure. 
Test and evolve your principles over time.

### Principles to Patterns
Is "clarity" a principle? Then never clip titles. Adjust a design to accommodate long titles.
Is "optimistic" a principle? Then the interface must be optimistic with very few delays.

## Chapter 3: Functional Patterns
---
These patterns are largely shaped by the domain a product belongs to. Patterns are different for a cooking site vs a financial app.

Patterns can be combined to create complex patterns that achieve a shared purpose (e.g. a card to encourage people to cook the meal shown). Patterns can also evolve (e.g. add a rating)

Patterns are the physical embodiment of the behaviors we're trying to encourage or enable through the interface. No need for 30 different product displays.

Through the interface we aim to help people accomplish certain goals and feel a certain way: learn a new recipe; focus on writing; feeling productions; feel inspired.

The purpose of the patterns needs to be thoroughly understood by the team. Only then can we make sure it is interpreted as intended by our users.

### Defining Function Patterns
Techniques:
-  Create a Pattern Map
	- Map your customers' needs, goals and motivations via customer experience mapping or "job to be done" exercises
	- Map your core modules to sections of a user journey. e.g. patterns for Discovery, patterns for Learning, patterns for Achievement
- Condust an Interface Inventory
	- Brad Frost describes the process
	- Do these regularly (every few months--a few hours each time), even if you maintain a pattern library -- new patterns will emerge
- View Patterns as Actions
	- What does the pattern do? "Promote a course" vs "image header" (so, let's name it "Billboard")
	- This connects the pattern to the behavior
- Draw a Pattern's Content Structure
	- List the core content a module needs to be effective. e.g. A Billboard needs a heading, a strong CTA, and an eye-catching background
	- Then, determine the hierarchy. Draw it out like a wireframe. This helps you structure the module, hierarchy and grouping of elements.
	- A wireframe can be given to a developer as the visuals are still explored.
- Place Patterns on a Scale
	- Place similar patterns on a scale, such as a visual loudness scale
	- Prevents modules from being recreated if you already have that "volume" created
- Treat Content as a Hypothesis
	- Do not start with the content with designing. Start with the purpose. e.g. Create a module designed for presenting product features, instead of using the actual content.
	- If the content consistently ends up being unsuitable for this patter, it's typically caused by one (or more) of these three reasons:
		- We didn't correctly define the purpose of the pattern.
		- We didn't design the pattern in a way that achieves its purpose best.
		- We're trying to force the content into a pattern that's not quite right for it.
	

## Chapter 4: Perceptual Patterns 
---
**Examples**: voice, typography, color palette, layouts, illustrations, iconography, shapes, textures, spacing, imagery, interactions, animations

These should live at the core of the brand. They should evolve with the product.

Spotify feels warm and personal laregly due to the imagery styles, color combinations (more black than green), subtil interactions, and typography choices.
Smashing Magazine feels payful, creative, and enthusiastic due to a bold color palette, illustrations, slight angles on interfaces.

Perceptual patterns express the brand through the interface and help to make it memorable.

The relationships between patterns is important as well. Typography relating to spacing. Colors relating to each other. Imagery relating to typography. etc..

When moving to other platforms (e.g native) perceptual patterns make the products feel like they are all part of the same brand.

If functional modules relfect what users want and need, then perceptual patterns focus on what they feel or do intuitively. 

### Exploring Perceptual Patterns
Consider creating a "design persona". An actual image of a person, to make it feel less abstract.

Come up with a few traits (five to seven) that best describe your brand, along with traits to avoid. 
Mailchimp: "Fun, but not childish. Funny, but not goofy. Powerful, but not complicated. Hip, but not alienating. Informal, but not sloppy."

Once you have traits, bring them to life through interface/

#### Exploration Techniques
- Mood Boards
- Style Tiles
	- A design deliverable that communicates the essence of a visual brand for the web
- Element Collages
	- An assemply of interface pieces that explore how branding works in the interface

### Iteration and Refinement
Trying the brand out in a more realistic setting of the interface often results in refinement of both perceptual and functional patterns.

### Balancing Brand with Consistency
Too much focus on consistency can stifle a brand.
If a design system only prioritiziez perfect consistency, it can be generic and rigid. A good design system strikes a balance between consistency and creative expression of the brand.

### Signature Moments
Loaders/catchy sounds/microinteractions can add additional layers of depth and meaning to an experience. While you systemize and structure design, don't leave out room for these details. There needs to be space to nuture and evolve these moments.

### Small-Scale Experiments
When exploring new styles, try them out on a small area of the site. If they work, gradually fold them into the system by applying them to other areas of the site. Be conscious of the role they play.

#### Balancing Brand with Business Requirements
Be cautious of adding features that don't sit comfortably with the brand, due to business requirements (e.g. a "new!" banner). If the requirement scales up, it may affect the overall design.

## Chapter 5: Shared Language
---
Creating cohesive design systems requires a shared language. 

"...groups of people can conceive their larger public buildings, on the ground, by following a common pattern language, almost as if they had a single mind." - Christopher Alexander, The Timeless Way of Building

Everyone - designers, developers, researchers, product managers, content producers - should have some degree of fluency. Fluency will improve over time.

Patterns should be connected by a shared language - a deeply rooted knowledge in the team about how to create and use design patterns for a particular product to create effective and coherent user experiences. This knowledge is propagated through a shared design approach, front-end architecture, brand vision, and daily practices such as collaborative naming, cross-discipline pairing, making patterns visible, conducting regular interface inventories, and maintaining a pattern library. The language should be evolved, strengthened, iterated and continuously tested.

#### Naming Patterns
Don't give object presentational names, e.g. a "pink" button can only be pink.

Sometimes it takes experimentation to find an approach that works. 

"The main problem with presentation based naming is that you can't find what you are looking for when the number of patterns in your library increases. It also give you no guidance or inspiration for when to use a specific pattern. People start to build more and more new patterns instead of reusing and enhancing the existing ones, which makes the problem worse and worse over time." -Tobias Ritterbach, experience owner, Sipgate

Atlassian names components after what users call them, e.g. Lozenges, Inline Edit. It drives user empathy.

A good name should be focused, memorable and embodies the purpose of the module it represents. It is the name people relate to and want to use. Effective names are metaphorical, have a personality, and communicate the purpose of a pattern.

#### Good Names Are Based pm Metaphors
e.g. Brackets support and strengthen a structure, like a building. A "bracket" component can be a small blurb that supports the main content.

e.g. A "spotlight" draws attention to a specific piece of content.

Names like "Progress Toggle Button" or "Binary Radio Button" don't work.

If no one remembers it, there's a high cahnce that people will recreate the pattern instead of reusing it.

#### Good Names Have a Personality
e.g. Small secondary buttons called "Minions"
e.g. Large button called a "Boss" (the main CTA)

These are easier to remember and can inspire other names. "Boombox" leading to a "Whisperbox" for a less distacting promotional element

#### Good Names Communicate the Purpose
Even a few good names can make your vocabularly more compelling, and the team will be more likely to use it and contribute to it.

If you find yourself struggling to come up with a name, changes are something isn't quite right. Maybe a module's purpose is unclear, or it overlaps with another's.

Eurostar had an interface used specifically to improve SEO. They named it "le blurb." Purpose: to provide interesting information aiming to improve SEO.

Approaching difficult naming decisions with humor can help.

#### Naming Collaboratively
Don't leave it up to the developers to name things. 

Ideas:
1. Set up a dedicated channel. Share a mockup/existing module, describe what it's for and what distinguishes it. Maybe share names you have come up with already. Discuss. Simply talking it through will strengthen and evolve the shared language. 
2. Test your language with your users. Try modules on paper cards that users can interact with. You may find your "prominents tabs" are ignored or that your "wizard control" is interpreted as a set of optional tabs. Use these as suggestions, not gospel.

#### Immersing Your Team in the Design Language
Ideas:
1. Immersing your team in the design language. Make it omnipresent. People who aren't initially interested will learn passively, through exposure.
	- Consider a pattern wall. Print and display screens of your product on a wall. Start with the most crucial/frequently used. Label the most prominent patterns.
	- Consider a Slack pattern bot. "Good morning! Just a reminder that this is... (Image of pattern with it's name)" (also, frontify.com will ping channels when a library is updated)
2. Refer to the objects by their names. It needs to be part of day-to-day conversation.
3. Name is part of the induction process. Perhaps a course with quiz.
4. Organize regular design system catch-ups. 30 mins with a well structured agenda. Weekly, then fortnightly. Teams can use this to agree on overarching patterns, such as icons or typography across the product. It's also a good opportunity to share new modules and dicuss their purpose, usage, and any problems and questions people might have.
5. Encourage diverse collaboration. Pair on designing and building patterns as much as possible. Pair design system fluent people with people less immersed in the system.
6. Keep a glossary. Use the same language from code to customer. See Intercom's glossary. Helps you get in a habit of vetting, discussing and articulating your language decisions as a team.

## Chapter 6: Parameters of Your Systems
---
What are some of the qualities a design system can have and ways in which risks and downsides can be managed?

Understanding which module to use should be easy. Don't make it easier to add a new one instead.

What shapes a system?
- The structure of your organization
- Your team culture
- The type of product you're working on
- Your design approach
- etc..

### Three Continuums
Where do you fall in these continuums?

#### Rules: Strict <--> Loose
**Example: Airbnb**
- Strict
- Design Language System is managed by separate team.
- Standardized Specifications: Modules are specified precisely. e.g. eight-pixel grid for spacing. Naming conventions are consistent. 
- Design is fully synchronized with engineering. Sketch files and code match, and are cross-platform. Syncing is a priority. 
- Strict process for introducing new patterns:
			1. A designer submits a proposal using a Sketch template with instructions about related behavior/rules. They suggest a suitable name and provide an example of how the proposed component can be used in context.
			2. The proposal then goes to the product support team via JIRA, along with the Sketch file. In many cases, the support team finds that a similar module exists already, or that an existing module should be updated.
			3. If a need for a new module is justified, the proposal goes to the DLS team, who consider the requirement and decide if the proposde design will work. Sometimes they use the proposed solution, sometimes they adapt or redesign it, to make sure it fits with the system.
- Comprehensive Detailed Documentation
	- Tools team built an automated process that generated screenshots and metadata about components, and publishes them to the guidelines site. Documentation is fully in sync with the Sketch file and code.

**Example: TED**
- Loose
- 5-6 people are resonsible for design system decisions
- Feel and utility take priority over perfect visual consistency
- Introducing an additional color or diverging from a standard layout is not a major concern
- Lo-fi paper sketches with rudimentary notes instead of detailed specs. Designers/engineers work collaboratively to bring it to life.
- Simple documentation. A collection of swatches instead of a comprehensive pattern library.
- Still, a deep mutual understanding of the purpose of patterns and their usages.

Stricter systems provide precise and predicatable outcomes and visual consistency. They can also become rigid, to the point that you start to make UX compromises for the sake of consistency.
- There should be opportunities outside the boundaries of the system, such as creative experiments and side projects. People need to be able to challenge rules. And that means rules need to be understood (i.e. documentation is essential).

Loose systems allow experimentation but can quickly become messy and fragmented. For it to work, everyone needs to be fully aligned on a product's purpose and design approach. It needs to be ingrained deeply into the culture. Even loose systems need solid foundations.

#### Parts: Modular <--> Integrated
Modularity means parts are interchangeable and can be assembled together in various ways to meet different or changing user goals.

Integration means parts are not interchangeable. There's connections between them that can't be fit in different ways.

**Advantages to modular approaches:**
- It's agile. Multiple teams can design/build modules in parallel.
- Cost-effective. Modules are reusable.
- Easy to maintain. You can fix a problem in one module without affecting others.
- Adaptable. Modules can be assembled in ways that meet different user needs.
- Have a generative quality. You can create entirely new outcomes by introducing new patterns or combining them in new ways.

**Advantages of integrated approaches:**
- Can be specific to a particular content, context, story, or art direction
- Tend to be more coherent and connected
- Can be built quicker as one-offs. No time needs to be spent on making the parts reusable.
- Easier to coordinate. Everyone on the team works towards one purpose.

	- Patterns should be modular and resuable, like Lego. But the extent of that should depend on your team.
	- Modularity may make sense at the implementation level, but not in the design (e.g. "Greendo" apartments). The extent of modularity should depend on what you're trying to achieve.

**The modular approach is best suited for products that:**
- Need to scale and evolve
- Need to adapt to different user needs
- Need a large number of repeating parts
- Have multiple teams working on them concurrently and independently
- **Example Products:** large-scale sites for e-commerce, news, e-learning, finance, government, etc..

**The integrated approach is best suited for products that:**
- Are designed for one specific purpose
- Don't need to scale or change
- Require art directions outside the boundaries of the system
- Have few shared repeating parts
- Are one-offs that are unlikely to be reused
- **Example Products:** creative conference websites, one-off marketing campaigns (even if the rest of your brand is modular), creative portfolios, showcases

**Drawbacks of modularity:**
- Building reusable modules is more time-consuming (use cases? how will they work across the system?)
	- Return on investment can take a long time
- Modules have to be fairly generic to accommodate a variety of use cases
	- To be innovative, you may need to have distinct content, a strong voice, or and effective use of perceptual patterns
- To make it worthwhile, teams may force the reuse of modules. Reusability > impact
	- Making modules connect seamlessly is hard.
		- To prevent this, don't just focus on modules, but focus on the connections between them as well: e.g. rules of how they relate to each other, their relative importance (i.e. loudness), their role in the user journey, their hierarchy in overall composition
		- The degree of modularization can change over time. You may start with just a few shared styles and principles. As is grows, you may notice more repeating patterns.

#### Organization: Centralized <--> Distributed
**Centralized Model**
- One group:
	- Defines patterns/rules
	- Has veto power over the system
	- Manages the pattern library and other resources where patterns are stored
- If someone if reponsible for it, it it more likely that the system will be curated, maintained and evolved.
- Creative direction is focused and opinionated, because it comes from one source
- This model is used in design-led companies like Apple and Airbnb
- May not work if each team has strong views on what their design should be.
- May bottleneck and slow down the entire product's life cycle.
	- At Airbnb, adding a new module can take up to two weeks.
- You can still have an open source model for contributions. Allow anyone to contribute. Actively encourage it.

**Distributed Model**
- Everyone who uses the system is responsible for maintaining and evolving it
- These tend to be more agile and resilient - if you team misses something, another can pick it up.
- Good for small companies
- Note: If someone is not in charge, work can slow down or stop completely. Instead of everyone contributing a little, you may just see a few people contributing a lot.

### Choosing the Right System

**Conway's Law:** Organizations which design systems [...] are contrained to produce designs which are copies of the communication structures of these organizations
- Team culture will be inevitably reflected in the design system

At the heart of every effective design system aren't the tools, but the shared knowledge about what makes good design and UX for your particular team and your particular product. If that knowledge is clear, everything else will follow.


## Chapter 7: Planning and Practicalities
---
Working on a design system as a side project is not enough. You need widespread support from your peers and senior stakeholders in the business.

### Getting Support from Senior Stakeholders
You need to demostrate that an effective design system will help to meet business goals faster and at lower cost.

**Examples of Benefits**
1. Time Saved on Designing and Building Modules
	- The first time you build a component in a modular, it can take twice as long as building a simple component. However, once you use it again, it is free.
	- Buttons can cost hundreds of thousands of dollars to design and build. 
		- "If your enterprise has 25 teams each making buttons, then it costs your enterprise $1,000,000 to have good buttons." - Nathan Curtis
		- http://smashed.by/goodbuttons
2. Time Saved on Making Site-Wide Changes
	- A bloated and inefficient system means that even the smallest changes are time-consuming and fiddly to make.
	- "Designed for Growth" article by Etsy's Marco Suarez
		- Tech and design debt slow teams down
	- Reusable patterns are updated automatically everywhere they are used.
	- Modules get better the more they are used
3. Faster Product Launch
	- Building a page using patterns from a library takes days. New designs take weeks.

The winning arguments tend to focus on demonstrating and quantifying the cost of inefficiency.

**Other Benefits**
- Brand Unity at Scale
	- A design system can unify a growing product or line of products
- Visual Consistency
	- A consistent visual representation helps people learn the interface quicker and reduce cognitive load by making things familiar and predictable. It helps make an interface feel intuitive. 
- Teamwork and Collaboration
	- You can build on another's work, instead of recreating the same things from scratch. Productivity improves.

Consider trying a design system on a small test project to see how effective it can be.

### Where to Start

#### Agree on Your Goals and Objectives
- What are the main outcomes you're hoping to accomplish? Clear goals = direction
- Include objectives with specific measurable steps

1. Systematize the Interface(s)
	- Define guiding design principles
	- Define and standardize reusable design patterns
	- Establish a pattern library
	- Set up master design files with up-to-date patterns
	- Refactor code and front-end architecture to support the modular approach
2. Establish Shared Processes and Governance
	- Set up knowledge-sharing processes through conversations, collaboration, pairing, training
	- Promote the pattern library and ecourage its use across the company
	- Promote shared design language across teams and disciplines
	- Make introduction to the design system part of the induction process

Create a simple roadmap to give the team a collective understanding of priorities and how the system will evolve.

Try to integrate design system stories into the main product's road map.

https://smashed.by/systemsroadmap

#### Make Your Progress Transparent
Teams who make their work public tend to progress faster. 
Knowing others may be using it as a resource may provide additional motivation.

Write blog posts about your successes, your mistakes, stumbling blocks, and things you'd do differently next time.

#### Create a Culture of Knowledge Sharing

**Examples**:
- Dedicated Slack channel
- Pattern wall
- Design system inducation as part of onboarding
- Regular catch-ups
- Encourage collaboration
- Workshops/tutorials on changes. Include the problems you are trying to solve and the solutions you implemented.
- Dedicate each day to a component in the interface (Vitaly Friedman would put a print out in the bathroom)

#### Keep Up the Team's Morale
Working on a design system is a long term process. You may not see the rewards for some time.

The reward is when you see others use your module and when someone comments on how helpful the information was for them.

Consider getting a lot of patterns up to 80% in a short amount of time. Then, spend time refinining. 

If you need to do an audit, get multiple teams involved. It will provide a shared sensed of ownership.
e.g. If you want living modules (code for modules in website match code in pattern library), consider adding screenshots first, and replacing them.

### Practice Thinking in Systems
You can't take an existing design and try to break it into modules. Each module needs to have a clear purpose, definition, name, role, needs to be reusable, etc..


## Chapter 8: Systemizing Functional Patterns
---
Products can be designed around similar user goals and needs, while encouraging entirely different behaviors (e.g. Slack supports collaborative styles of work)

Think of behaviors when connecting patterns with the design intent and ethos of the product.

### A Purpose-Directed Inventory
The purpose of an inventory is not to account for all of the visual inconsistencies, but to define the most essential design patterns and get mutual understanding in the team on how they should work across the system.

A typical outcome of an inventory: a list of elements that need to be standardized, along with some sketches and ideas of how patterns should be defined.

When you group things by purpose (the behaviors they're designed to encourage or enable), you might end up with things in the same group that look very different. 

Don't focus on making all of the buttons look consistent. Focus on using the correct component to communicate purpose. (e.g. link vs button)

### Preparation
- Do an audit after foundational UX work, like user research, content strategy, information architecture, design direction
- Both designers and front end developers should take part, but include back-end developers, people with content backgrounds, and product managers as well. Should be 4-8 people. If more people are needed, do it with representatives from different disciplines. 
- Identify key screens/user flows that are fundamental (10-12 screens) when getting started. Have two printouts, hang one on the wall and cut the other one up. The first printout will keep you focused on the bigger picture.

### Step 1: Identify Key Behaviors
Identify the key user needs and behaviors you want to support at each segment of the user journey.
- For a small app, just a few screens and different states of the same screen
- For larger products, group pages into segments of the journey
- If you have too many pages in the same segments supporting similar behaviors, it's an indication that your information architecture might need work
- If a screen supports several behaviors, the most important actions should be clear and not in conflict with one another

Pay attention to wording. Instead of designing of "retention", design for "engagement". Instead of designing of "promotion", design for "discovery".

Successful businesses join user goals with business goals.

Break down behaviors into actions. 
The actions for "Book discovery" can be:
- Scan for interesting books
- Refine list of recommended books
- Control how the list is presented
- View and learn about a book
- Make a selection of books you might like
- Shortlist and reserve books

### Step 2: Group Existing Elements By Purpose
Taking one behavior at a time, look across all the pages to find the elements that support it. e.g. "View a book" may look different in different places
- Cut them out and arrange in group
- These are candidates to be defined as patterns

### Step 3: Define Patterns
Decide how to deal with the items in each group. Should they be merged into one pattern, or kept separate?

**Techniques:**
- Specificity Scale
	- Generic (e.g. exhibitions) vs Specific (e.g. content block)
	- This can be tricky. Too specific and it's harder to maintain/keep consistent. Too generic and it looks like a more generic design.
	- What are you trying to achieve?
- Content Structure
	1. List the core content slots a module needs to be effective (e.g. is an image essential?)
	2. Determine the hierarchy of elements and decide how they should be grouped (e.g. is the icon part of the key info or the image?)
	3. Make sketches to visualize the structure. Find the optimal design.
	- If elements share the same structure, it can be merged into the same pattern.
	- If you struggle to unify structures without compromising their purpose, then they probably shouldn't be merged.
	- Similar structures, but need to look/behave differently? It's a variant. There's a default pattern + additional styles

When assessing your groups of patterns, thing "If I change this module, do I want the others to change in the same way?"

Looking at the relationship between the content structure and styles can increase the reuse of more patterns. e.g. "book title" --> "large title"/"small title"/"small metadata"

### Naming
Try to find a name that reflects a pattern's position on the specificity scale. e.g. Use "Page Tabs" instead of "Course Tabs". If it's generic, it can be used elsewhere.

Naming should be a engineering/design joint decision. Effective names guide usage and reduce the chances of duplicate patterns.

### Granular Patterns
Repeat the process for more granular patterns like buttons, links, headings, etc..

If you have elements with similar purposes, think of them in relation to each other rather than independently. How are buttons different from links? 

#### Links vs Buttons
A link navigates the user away from the current page. 
A button submits an action and toggles something in the interface.
We, however, often mark up links as buttons.

In IBM's Carbon, links are a navigational element. Buttons are only used if the user’s action will change or manipulate data.
In Shopify's Polaris, buttons can represent any type of action, including navigation. Links are used both for embedded actions and for navigation.

The most important aspect is a consistent expression of purpose.

Heydon Pickering in Inclusive Design Patterns: differentiate between links and CTAs, rather than buttons and links. Button CTAs and link CTAs look similar, but have subtle differences.

![[cta-buttons-links.png]]

Standard links are typically optional information that is embedded within content. CTA links are more important.

### Visual Hierarchy
In Marvel’s design system, “flat” buttons are used to signify “necessary or mandatory actions”; “ghost” buttons are used to signify “optional, infrequent or subtle actions.” Flat buttons can be used alongside each other, when actions are equally important.

In other systems (Atlassia, Polaris), primary buttons should only appear once per screen, to represent the most important action in any experience.

Polaris: "Plain", "Outline", "Basic", and "Primary" buttons. The default is "Basic". Other styles are used only if a button requires more or less importance.


### Special Cases
Special cases will always occur. Giving them generic names will be difficult. You may just need to give it a memorable name instead. (e.g. a "Mark Complete" button that is animated)


## Chapter 9: Systemizing Perceptual Patterns
---
What's important is not the styles themselves, but the effect they have. A functional tool should be useful, usable, and feel simple, safe, robust.

A set of shared colors is not enough. You also need a shared use of color in the context of the product. Links and headings should not be the same color.

### Signature Patterns
A signature patterns exercise is useful to get the whole team on the same page, especially if they're not used to thinking about perceptual patterns.

Think not only about the properties but also high-level principles, combinations of different elements, and the relationships between them. Ask each person to write down the most memorable and distinct elements that make your product feel a certain way. e.g. "The pick tick always makes me smile."

It's also helpful to identify places where the design is off brand.

Your final list may have:
- Voice/Tone
	- "Direct, positive, encouraging tone of voice"
- Color
	- "Primarily white color palette with pink accents"
- Shapes
	- "Mostly square and occasionally circular shapes"
- Typography
	- "Generally large typesize, focus on readability"
	- "Europa typeface"
	- "Generous whitespace"
- Animations
	- "Small, soft movements or no movement at all"
	- "Pink to blue subtle hover, opacity, gradual color change"

### Identifying Perceptual Patterns
Get your list of styles (e.g. color, typography, interactice states) and systemize them:
1. Start with the purpose
2. Collect and group existing elements
3. Define patterns and building blocks
4. Agree on the guiding principles

There will be overlaps and this is good. You can build on the properties you’ve defined previously and you can see how they relate to each other. e.g. colors in interactive states are used in animations

### Color
The first step is to make the use of color more consistent. Start by listing the roles color plays in your interface. Color can be used to highlight links/actions, draw attention to messages and differentiate between them, etc..

Create a doc with categories containing the following: type (e.g. CTA), example, value (e.g. hex value), feel. Fill it in with the various color instances that fit the category. You may have 10 links that all look different.

![[color-audit.png]]

Next, define the patterns. When do you use blue links and when gray? (e.g. primary vs support nav vs CTAs related to support, etc..)

The decisions you make here can alter the overall aesthetic of the site. If you decide all CTAs should be red, there will be a lot more red on the site. Some changes may alter the brand's feel, so you may want to avoid.

Create color palettes that are focused, precise, and accessibile. Reduce the number of variables for each color. 8 shades of blue can be reduced to a base, light, and dark version. Start with the colors that you absolutely need. Then, add shades. You may need many if you support dark and light mode, in order to provide sufficient contrast. Shades should be 20% darker/lighter than base, and so on.

Finally, agree on a few basic principles for color usage, e.g. "always use accessibile color contrast" or "We allow our great content to be the color that brings the page to life. We do not color code our sites, or sections within our sites."

### Animations
These follow the same process: start with the purpose, collect and group existing styles, define patterns and building blocks.

Purpose can be to soften transitions, add emphasis to specific information, or to hide/reveal extra information.

The feel of an animation is important. Quick, soft, bouncy motion can be percieved as lively/energetic. Slow ease-in-outs feel certain and decisive.

When auditing for animations, you can capture them with Quicktime. List the effect (e.g. color change, scale up), example, timing and easing, properties (e.g. opacity 0 -> 0.15), and feel.

Figure out what tones you want to strike throughout the system and try aligning all animations to them.

Two elements animated at the same speed can feel completely different if they are different sizes or travel different distances.

Instead of just a single value, start with a base and provide several incremental steps. For example, if the base time is 0.5 seconds, smaller items that travel a shorter distance (such as an icon scaling up) would take less time. Items that travel longer distances (such as a menu popping up) would require more time. A full-screen transition would be one or two incre- ments above the base value.

At the very least, define some general principles, such as "don't let the animation get in the way of completing a task" or "keep timing short and the motion subtle"

### Voice and Tone
For users like blind users, the experience of digital products often come through the form of style of writing. Design, brand and marketing teams need to coordinate their efforts when defining patterns.

When auditing voice and tone, also consider how people speak in meetings and in informal conversations.

MailChimp’s Voice & Tone is one of the most effective examples of how language patterns can be defined. The tone shifts to respond to the emotional condition of the user. Humorous & lighthearted to serious & practical

## Chapter 10: Pattern Libraries
---
A pattern library is not the system itself, but a tool for documenting and sharing design pat- terns. To be effective, it needs a system foundation at its root. 

Multidisciplinary pattern libraries are more resilient and enduring.
Making a pattern library comprehensive and up to date is impossible without designers’ active involvement. Patterns designed without the content team's input also fall apart.

### Content
A folder in Google Docs is like an MVP pattern library. Once you have the content, it will be easier to figure out how the website for it should be designed and built.

Get your core patterns and definitions down quickly. Build the pattern library website later.

### Organzation of Patterns
Just have a common methodology to start. The structure of it can be changed later. You'll figure out how you want to find things as you pattern library grows.

#### Abstracting Perceptual Patterns
Function patterns = components
Pereceptual patterns = styles (or foundations or design tokens)

#### Organizing Functional Patterns
If team members don’t know that a pattern exists or can’t find what they need, they are likely to create a new one or go outside the pattern system.

**Organization Options**:
- Alphabetical
	- Works until the list becomes too large
- Hierarchical
	- Atomic design is one approach to categorization
		- Too much time can be spent on debating what elements belong to what category
		- You may just want to do atoms and molecules
- By Purpose or Structure
	- e.g. Promotional, Explanation, User actions, Social, Text/content blocks, System messages, etc..

Find a structure that’s right for *your* team. 

### Pattern Documentation
Start with a lightweight overview of the main patterns. Once you have a simple foundation, you can improve the pattern library over time, by adding features and information the team needs. 

#### Documenting Function Patterns
Documenation should be focused and easily scannable.

Start with the basics:
- Name
	- Display it prominently. It should stand out from the rest of the content.
	- You should be able to glean the purpose from the name without needing to read the description
- Purpose
	- People typically skip descriptions, especially long ones. Focus it. Use one or two sentences to explain what a pattern is and what it's for. Don't be vague.
		- e.g. Good: “Fact Grid is a shortlist of facts or bits of interesting information. Use Fact Grid to give the reader an immediate impression about the upcoming content.”
		- e.g. Too Vague: “Use Showcase to present multiple types of information with a media file.”
	- You can add some design/content recommendations
		- e.g. "Maximum of 3 lines per fact"
- Example (visual and code)
	- Use self-documenting examples. 
	- Show multiple variants and use cases.
	- The UI copy should guide usage further. A Billboard should have copy/images that match it's purpose.
	- Living instances are usually preferred (instead of a screenshot)
- Variants
	- We need to see what options are available, as well as how the options are different from one another.
	- Just showing a solid and a ghost button is not enough. You need to know you should use one over another.
	- Atlassian describes each button and when to use it.
		- e.g. Icon and Label: Use when you want to draw more attention to the button, or when an icon helps to convey more meaning.

Additional Information:
- You may need to add versioning of components
- You may want to list team members involved in the creation of a pattern (gives them a sense of ownership and helps with future development)
- You may want to show related patterns if the pattern is not quite what you're looking for. This reduces the change of patterns getting duplicated.
	- e.g. Not what you're looking for? To group similar conepts and tasks together, use the card component. To create page-level layour, use the layout component. etc.. (Shopify Polaris)

Taking The Pattern Library To The Next Level by Vitaly Friedman has checklists for what patterns to document and what to include with each pattern

#### Documenting Perceptual Patterns
The focus tends to be on building blocks - color palette, typographic scale, etc. - but we need to know how these properties are used and how they work together.

**Examples:**
- GOV.UK group colors under categories like text, links, backgrounds, buttons, focus.
- Polaris points out dos and donts like how blue shouldn't be used for buttons.
- US Gov shows type pairings and recommended usage.
- Carbon, in addition to separate color documentation, has a tab with the styles for a specific modules on each component pages

Document your interactive states as well. Display different modules under normal, hover, focus, and selected states.

Document the relationships between elements (between colors, between typography and spacing, etc..). Too much of one color creates a different feel. Display spacious vs normal vs compact modules.
- Spacious modules may be used for promotional purposes
- Compacy modules may be used for supporting functions

Instead of isolated on separate pages, styles should be presented in a way that shows how they work together, and that highlights signature patterns and the relationships between various elements.

### Workflow
Teams with effective pattern libraries have systematic approaches ingrained in their workflow. Some teams, like Airbnb, have strict and precisely specified processes with powerful tooling. Others are much more informal.

#### Process for Adding New Patterns
Agree on how new patterns will be added to the system.

**Example (Nordnet)** [Link](https://nordnet.design/super-easy-atomic-design-documentation-with-sketch-app-c5eb9df0f6fa):
1. Submit a design to the UI kit folder on Dropbox
2. Discuss the inclusion of the pattern as a team
3. Document any included designs inside the UI kit. Add the new design to the Craft Library which will automatically roll out to the entire team.

Meet every 2 weeks to discuss new submissions. Go through a Trello backlog and decide if a module should be approved for inclusion or archived.

[Process at Shyp](https://medium.com/shyp-design/managing-style-guides-at-shyp-c217116c8126)

To make sure the format of submissions is consistent, have a standard template with simple guidelines, such as name, description, author, and date. What is it? What is it for? How does it achieve its purpose?

#### Criteria for Adding New Patterns
Have shared criteria for adding (and also updating and removing) patterns.

**Possible Approaches**:
- Every new element on the site is also automatically added to the pattern library.
	- First, check if a similar pattern exists already, or if an existing one can be modified.
- Elements are added only when they’re reused. 
	- Add modules only on the second, or even third use. 
	- An element has to prove itself as a pattern before being added to the system, which helps to keep the pattern library lean. With this approach it’s important to have visibility of everything being created and effective communication across teams.
	- A log of undocumented patterns should also be kept, so the team has full visibility of what’s available, even if it’s not in the pattern library.
- Elements are added on potential reuse
	- If an element is defined in a generic way, it is more likely to be reused in the future. 
	- If a new component has a specific purpose (such as a seasonal promo, a module related to a specific event, and so on), it can be treated as a one-off.
		- Only make specific components if absolutely necessary. It should be explained why a module needs to be specific.

#### People and Responsibilities
If everyone contributes to a design system, the designer and developer who create a pattern are responsible for adding it to the pattern library. 
Sometimes you need a person or group of people responsible for curating and maintaining the pattern library, even if everyone contributes to it.

A dedicated systems team should have one of these roles (or both):
- **Curator**: If a submitted pattern doesn't meet standards, the team encourages the designers/developers who created it to change it, rather than making the change themselves. (Atlassian does this)
	- Best for distributed teams with looser system structures
- **Producer**: The team accepts submissions across the company, but have final say over what is included, adjusted or removed. Designers will work closely with designers in different teams to give feedback, ask questions, or propose missing patterns. (Airbnb does this)
	- Best for stricter more centralized systems

It's important that systems teams are seen as partners, rather than police. Don't create a situation where people go away and do a lot of work only for you to veto or approve it.

#### Aligning Facets of the System
The team following the same approach across facets -- naming, structures, understanding of purpose -- is more important fully synchronizing your patterns.

Be consistent in naming/folder structure across your design files, component library, and code.

Designers at Nordnet even follow BEM naming conventions in design files, so developers and designers speak the same language.

Alignment makes sychronization easier.

### Tools
There are a few different approaches to keeping a pattern library in sync with production code.

Note: Full synchronization between a pattern library and code is extremely difficult to achieve. In stricter systems and centralized organization it is more important, whereas companies with looser structures are more tolerant to having it out of sync.

#### Keeping the Pattern Library Up To Date
Methods:
- Use CSS documentation parsing tools such as [KSS](https://warpspire.com/kss). Comments in CSS --> script --> markup is generated
- Style Guide Generators like [Pattern Lab](http://patternlab.io)
- [Fractal](http://smashed.by/fractal) by Mark Perkins helps build and document a pattern library - is flexible and unopinionated
- 
#### Keeping Master Design Files Up To Date

#### Pattern Library as the Source of Truth

### The Future of Pattern Libraries

