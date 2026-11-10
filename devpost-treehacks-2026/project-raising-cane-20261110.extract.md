- 
              
- 
      
        
    
      
  *Full CAD model of Shepherd*
- 
      
        
    
      
  *CAD model of omniwheel handler*
- 
      
        
    
      
  *CAD model of cane's handle*
- 
      
        
    
      
  *Lidar*
- 
      
        
    
      
  *Final handle version with motor bridge*
- 
      
        
    
      
  *Motor Bridge inside Gen 1 handle*
- 
      
        
    
      
  *Haptic feedback system pre-electrical tape*
- 
      
        
    
      
  *Haptic Feedback Engine*
- 
      
        
    
      
  *Lithium Ion Batteries*
- 
      
        
    
      
  *Battery Management System* 
- 
      
        
    
      
  *Heat Shrunk Li-Ion batteries*
- 
      
        
    
      
  *3D Printing first gen of omniwheel handler*
- 
      
        
    
      
  *Motor Bridge*
- 
      
        
    
      
  *ESP32*
- 
      
        
    
      
  *Accessible route navigation with added waypoints for stable routing*
- 
      
        
    
      
  *Omni-wheel and Shrek shoes*
- 
      
        
    
      
  *Wago connectors*
- 
      
        
    
      
  *Person classification*
- 
      
        
    
      
  *Bounding box for grass/bush detection*
- 
      
        
    
      
  *Bluetooth pairing / Debug tab*
- 
      
        
    
      
  *UI for testing obstacle detection and steering commands.*
- 
      
        
    
      
  *Horizontal view showing views to expand Lidar/Cam/Terrain/Navigation/Agent - Helpful for dev*
- 
      
        
    
      
  *Us with Shepherd post demo-vid filming*

## Inspiration

1.7 million Americans are legally blind. While canes tell you something is there, they don't tell you where to go. Smart canes actively steer you around obstacles, but they’re prohibitively expensive and inaccessible. We wanted to create a cheaper, intelligent alternative.

## What it does

Shepherd is a motorized smart cane that uses iPhone LiDAR and computer vision to detect obstacles and physically guide the user away from them in real time, and is also able to navigate users using GPS with an interactive voice interface.

A small motor on the cane applies lateral force, nudging the cane left or right, so the user feels which direction is clear without needing audio cues or screen interaction. The system runs a 60fps depth pipeline: LiDAR frames are split into left/center/right zones, a gap-seeking steering algorithm computes the clearest path, and a 12-byte BLE packet is sent to an ESP32 motor controller every 100ms. The motor uses a leaky integrator for smooth output. End-to-end latency from obstacle detection to motor response is under 50ms.

Shepherd also does GPS pedestrian navigation with turn-by-turn voice guidance and waypoint following (wheelchair-accessible routing that avoids stairs), person detection via Apple's Vision framework, terrain detection (grass/mulch avoidance using HSV color analysis of the camera feed), and haptic proximity feedback that pulses faster as obstacles get closer.

## How we built it

Hardware: Seeed Studio XIAO ESP32-S3 driving a DC motor, mounted on a standard white cane. The iPhone sits in a phone mount on the cane handle. The battery is inside the pipe, the motor and wheel sit at the bottom, and an enclosure in the handle has the rest of the electronics and wiring.

iOS app (Swift 6 / SwiftUI): ARKit captures LiDAR depth maps at 60fps. An ObstacleDetector samples the depth buffer on an 8-pixel grid across three horizontal zones, filtering floor pixels using depth gradient heuristics. A SteeringEngine computes a continuous steering command (-1 to +1) using inverse-depth-weighted lateral bias, with EMA temporal smoothing (alpha=0.08) to prevent hallway oscillation.

Communication: 12-byte BLE packets (Float32 angle, Float32 haptic distance, UInt32 mode) sent at 10Hz. The ESP32 normalizes the steering value by dividing by 255 and feeds it through a leaky integrator (tau=0.55s) before driving PWM output.

Navigation: Google Geocoding API for address resolution, OpenRouteService wheelchair routing profile for step-free pedestrian directions, Overpass API for crosswalk/traffic signal detection. Micro-waypoints interpolated every 6m along the route polyline with ARKit+compass heading fusion for steering bias.

Terrain detection: We use HSV color analysis of the camera buffer. We scan the lower 60% of the YCbCr frame for green-dominant pixels (hue 60°-160°), map them to left/center/right zones, and inject virtual obstacles at 1.5m in zones with >15% grass coverage. Works at night because HSV separates hue from brightness.

## Challenges we ran into

Hallway oscillation was the first real problem; the cane would jitter left-right-left in narrow corridors because both walls were equidistant. We solved this by replacing repulsive steering with a gap-seeking algorithm that analyzes 16 depth columns, finds the direction of maximum clearance, and outputs proximity-scaled commands toward the clearest path -- solving overcorrection by steering toward safety rather than away from danger.

A similar problem was deciding direction when encountering dead ends. We solved it with EMA temporal smoothing: instead of reacting to each frame independently, the steering engine maintains a running average of zone distances, so it commits to a direction rather than flip-flopping endlessly.

Coordinate transforms between ARKit's raw depth buffer (landscape orientation) and the phone's portrait display took several iterations. The depth map's X axis is inverted relative to the display, so "left zone in raw coordinates" is actually the right side of what the user sees.

The ESP32 motor would keep spinning if the iPhone app crashed or BLE dropped. We added a frame watchdog (500ms timeout) on the iOS side and a packet timeout (250ms) on the ESP32 side that both independently zero the motor.

Fine-tuning PID control was another element requiring constant adjusting; we navigated through this by including dynamic debug sliders within our application during development to manually control and test our many parameters dictating movement.

## Accomplishments that we're proud of

The steering actually feels good. Not jerky, not laggy -- it applies a smooth lateral force that you can feel through the cane without thinking about it. Getting from "technically working" to "physically intuitive" required a lot of parameter tuning (base scale, EMA alpha, deadband thresholds, leaky integrator tau) that doesn't show up in the code but makes all the difference.

The entire obstacle detection to motor response pipeline runs in under 50ms. That's fast enough that the cane reacts before you walk into something. The grass detection works without any ML model. Just pixel color analysis. It's dumb and it works.

## What we learned

LiDAR is remarkably good for this use case -- sub-centimeter depth at 60fps with zero calibration. The hard part isn't sensing obstacles, it's deciding what to do about them. The steering algorithm went through five major rewrites.

BLE latency matters more than bandwidth. Switching from "write with response" to "write without response" cut our effective latency in half.

Spot-welding was another timeless classic that we can proudly say is now part of our repertoire.

## What's next for Shepherd

Proper terrain segmentation using a fine-tuned model instead of color heuristics -- mulch and brown grass would be nice to detect. Integration with transit APIs for multi-modal navigation (walk to bus stop, take the 22, walk to destination). And making the hardware smaller -- the current ESP32 + motor setup works but isn't something you'd want to carry every day.

## Built With

- accelerate
- arduino
- arkit
- avfoundation
- ble
- c++
- combine
- corebluetooth
- corehaptics
- coreml
- deeplabv3
- freertos
- gcp
- nimble
- swift
- swiftui
- vapi
- vision
