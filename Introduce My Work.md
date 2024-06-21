
After graduating, I have four work experiences.
I began my career in the advertising department at  Bytedance in 2016, I worked on a website building tools called chengzijianzhan and conversion tracking platforms.

Following that, I worked as a front-end game engineer at Shopee.  

From 2020 to 2023, I was a front-end development engineer at ByteDance's Lark division, where I developed spreadsheets and  maintained a canvas rendering engine. 

Finally, I worked at a small design tool startup company called jsdesign, where I focused on performance enhancements.

I made a poor decision to leave the startup for an offer from a larger company, but they rescinded the offer.
## Detail

I am going to describe my main work experience at Lark's Spreadsheets.
Our group maintains a canvas rendering engine called Faster, which renders the view layer of our spreadsheets.

Project1: Following the layout methods in Flutter, I added layout functionality to the canvas engine. I added multiple layout components, Like Flutter, each component serving a single purpose.  Our canvas engine also supports JSX syntax, which reduces the burden of using a canvas engine for feature developers.  This layout capability improves development efficiency , so that feature developers don't have to manually calculate node positions.


Project 2: Multiple canvas engine integration.  Because we are upgrading our canvas engine, the new version of the engine has layout capability, and the lifecycle is different from the previous one. We have to gradually upgrade spreadsheet's view layer, so there are two different versions of engine in the same time, and they can be nested with each other.  This main task of this project is to normalize the interfaces between the different versions of the engine, so that they seem to work as one engine. Both canvas can response dirty-marking, layout change, drawing and mouse events. 

Project 3: Upgrade Spreadsheet SSR solution: Moving from the previous canvas solution to SVG solution. The server-side rendering produces SVG data that can be directly rendered by the browser, eliminating the need to wait for the engine code to load and then render to the canvas. The overall performance of SSR TTV has improved by 200-300ms.

Project 4: Self-developed FPS reporting tool, which can detect the screen refresh rate through the browser side.  At the same time, I added Jank judgment according to game industry.  This monitoring tool can answer the questions of many business parties about the FPS jitter of the view layer. Thus, the team can reduce the effort to locate the cause of FPS jitter.

## Detail2
There is one project in this work experience that deserves to be described.
I explored different forms of game interaction and completed a game that recognizes when a user steals a bun by analyzing the shape of their mouth.  The front-end of the game was developed using React Native and Google ML Kit, and Pixi.js was used to finish the game.

The project was challenging because it required breaking down an abstract and vague goal into a concrete and usable project.


## Detail3
I began my career in the advertising department at Today's Headlines, called Bytedance now, I worked on a website building tools called chengzijianzhan and conversion tracking platforms.
I refactored the conversion tracking platform, switching from Python to Golang due to the old service having too many dependencies to maintain.   Additionally, I adopted the GraphQL API specification as the interface to the new system. The reason I choose GraphQL because it has its data types and can update interface documentation in real-time.

