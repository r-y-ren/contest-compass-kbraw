## Inspiration

The United States is facing a growing shortage of surgical specialists, projected to reach nearly 20,000 by 2036. Additionally, operating rooms cost roughly $2,200 per hour per surgeon. Today’s robotic surgical robots greatly improve precision by scaling and filtering human motion. But they do not automate surgical tasks. This is due to a lack of reliable AI spatial vision understanding and AI robot communication. Instead of making surgeries more accessible, current robots only make them more complex and costly.

## What Robosurge does

Robosurge is a remotely controlled integrated surgery platform. It allows for a single surgeon to perform entire complex procedures autonomously from anywhere in the world.

In traditional surgeries, an attending surgeon commands a large team of assistant surgeons. The attending surgeon syncs to Robosurge’s camera arm via a Meta Quest 3 VR. Then the surgeon can command an array of magnetically controlled robotic arms using natural language. This architecture reduces required personnel from 10 -> 1, and distance to patient from meters to anywhere around the world.

## How we built it

We built the entire system from the ground up, from the basic kinematics systems to the frontend web control panel.

- **Inverse Kinematic Control System** : We built a custom inverse kinematic control system for the robot arms, bypassing limitations of the provided SDK and improving speed while adding safety guarantees.
- **Custom Depth and Coordinate Mapping** : We built our own coordinate resolution system using an Intel RealSense Depth Camera and ArUCo tags for consistent motion across all four arms. We then perform an affine transformation between the coordinate space of the camera with the coordinate space of the robot arm to give precise control to the robots.
- **Multilayer AI Computation** : We use both Meta’s Segment Anything Model (SAM) and YOLOv11 on edge and an ASUS Ascent GX10 to identify and segment objects. We also perform LLM fusion between GPT-5.2 and Gemini Robotics-ER 1.5 for two levels of visual understanding as well as individual move orchestration.
- **Realtime VR Position Mapping** : Using real-time orientation data from a Meta Quest 3, we are able to have a live head track with a physical moving camera on a robot arm. Built with a websocket server, a real-time MJPEG video stream, and a custom rotation translation algorithm
- **Dynamic Frontend** : A TanStack and FastAPI powered web app supplies real-time video feeds using WebRTC, with WebSocket and REST API for real-time diagnostics and controls.
- **Reproducible Builds** : By using Nix in both our production and development environments, we are able to guarantee reproducible builds of our robot control systems using a purely functional package manager which operates the same regardless of system state.
- **Theorem Proving and Formal Verification** : We used dependent type theory in Lean to prove that trajectories are within safe physical bounds while tracing out the shape of a lemniscate, sinusoidal longitudinal wave, vertical spring (evolving according to Hooke’s law), and helix. We also created a custom solver for systems of autonomous differential equations using the Runge-Kutta method, used to trace trajectories in the Lorenz and Rössler attractors. (btw this entire portion was written with NO AI)

## Challenges We Ran Into

Our primary challenge was navigating a high-intensity 36-hour learning curve; despite having no prior robotics background, our team successfully mastered multi-arm kinematics and hardware integration. Lacking specialized 3D scanning hardware, we engineered a precise coordinate positioning stack using ArUco markers and custom affine transformations to map the Intel RealSense 3D feed directly to the robotic joint-space. We also addressed the inherent 3D spatial reasoning limitations of current LLMs by developing robust grounding harnesses to translate high-level natural language intent into reliable physical actions. We also had to deal with precision issues when attempting to trace out famously chaotic systems, which required us to implement Runge-Kutta (RK4) numerical methods within Lean. The varied backgrounds of our team and our different approaches to tackling these issues led to the three-part development of our codebase, with a primary focus on agents, a secondary focus on augmented reality, and a tertiary focus on formal verification and theorem proving.

## Accomplishments that we're proud of

As a team of first-time hardware engineers, we are really proud of what we have built. In 36 hours, we built a full inverse kinematic system, 3D depth mapping, and agentic robot control. We successfully designed, cut, and assembled a custom robotic frame and 3D CAD model to house our four-arm station. This is also very likely one of the first times the Lean theorem prover has been used so extensively in a project of this scale at any hackathon. (oh yeah, did we mention we use Nix, btw?)

## What we learned

We learned how to work extensively with computer vision, formal verification, and hardware-software integration, all of which were incredibly challenging yet rewarding. We discovered that integrating VLMs with physical hardware involves a steep learning curve, especially in building a 12-DOF coordinate system that allows an AI to see a pixel and touch a target perfectly.

## What's next for Robosurge

Firstly, we want to integrate haptic feedback into the VR controllers so surgeons can feel another layer of information, e.g. actually feel tissue resistance as they move the robotic arms. Secondly, we plan to expand the system to support a multiple-control copilot-based model where a single surgeon can oversee multiple operations, where we can gain further efficiency by parallelizing the entire process. Finally, we hope to move to high-fidelity medical simulations and complex anatomical models. When robots start performing the bulk of the grunt work actually involved in an operation, procedures can be simulated rapidly and accurately, decreasing the failure rates of operations through better training.
