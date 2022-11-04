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
Creating cohesive design systems requires a shared language. 

"...groups of people can conceive their larger public buildings, on the ground, by following a common pattern language, almost as if they had a single mind." - Christopher Alexander, The Timeless Way of Building

Everyone - designers, developers, researchers, product managers, content producers - should have some degree of fluency. Fluency will improve over time.

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
3. Immersing your team in the design language. Make it omnipresent. People who aren't initially interested will learn passively, through exposure.
	- Consider a pattern wall. Print and display screens of your product on a wall. Start with the most crucial/frequently used. Label the most prominent patterns.
	- Consider a Slack pattern bot. "Good morning! Just a reminder that this is... (Image of pattern with it's name)" (also, frontify.com will ping channels when a library is updated)
4. Refer to the objects by their names. It needs to be part of day-to-day conversation.
5. Name is part of the induction process. Perhaps a course with quiz.
6. Organize regular design system catch-ups. 30 mins with a well structured agenda. Weekly, then fortnightly. Teams can use this to agree on overarching patterns, such as icons or typography across the product. It's also a good opportunity to share new modules and dicuss their purpose, usage, and any problems and questions people might have.
7. Encourage diverse collaboration. Pair on designing and building patterns as much as possible. Pair design system fluent people with people less immersed in the system.
8. Keep a glossary. Use the same language from code to customer. See Intercom's glossary. Helps you get in a habit of vetting, discussing and articulating your language decisions as a team.


#### Immersing Your Team in the Design Language


## Chapter 6: Parameters of Your Systems

## Chapter 7: Planning and Practicalities

## Chapter 8: Systemizing Funcational Patterns

## Chapter 9: Systemizing Perceptual Patterns

## Chapter 10: Pattern Libraries



