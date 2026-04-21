# AAE5303_Reflection_Report

**Student Name:** MAN Chi Lok Louie

**Student ID:** 25001004G

**Group Number:** 我們組 (WMZ)

**Date:** April 21, 2026

##Section 1: AI Usage Experience
During the AAE5303 project, my interaction with AI tools like Gemini and Google AI was shaped significantly by my unique technical setup. Unlike the majority of students who utilized the provided Docker containers on Windows, I insisted on running the Visual SLAM pipeline natively (via UTM VM) on a MacBook Pro with Apple Silicon (M1 Pro). This decision rendered the "Cursor" tool less useful for direct environment linking, so I relied on Gemini as a high-level research and debugging partner.
I used AI primarily for three tasks: environment configuration, parameter estimation, and presentation structuring. I used these tools roughly 3–4 times a week during the peak development phases of ORB-SLAM3. The most useful feature was the chat-based troubleshooting for Linux-specific compilation errors and the ability to process external documentation links (like the HKU MaRS drone CAD files) to help estimate extrinsic parameters for the IMU. Because I was working in an ARM-based virtualized environment, I often had to "translate" AI suggestions meant for standard x86 systems into solutions compatible with my Ubuntu 20.04 VM on UTM.

---

##Section 2: Understanding AI Limitations
A critical instance where AI produced misleading results occurred when I attempted to refine the IMU extrinsic parameters to reduce trajectory drifting. The ORB-SLAM3 output was drifting vigorously to the left and upward. I prompted Gemini to suggest a refined Tbc (Body-to-Camera) matrix based on the AMtown02 drone's CAD layout.
Gemini provided a revised matrix with modified rotation values, claiming it would compensate for the perceived misalignment. However, when applied, the drift became even more "vigorous." I detected this issue through immediate visual inspection of the trajectory in the ORB-SLAM3 viewer—the drone's path deviated significantly from the visual evidence in the AMtown02 video stream.
The failure happened because the AI lacked the physical intuition of the specific coordinate system conventions used in the HKU MaRS dataset versus the standard OpenCV conventions expected by ORB-SLAM3. It "hallucinated" a geometric correction that looked mathematically plausible but was physically inverted. I eventually had to fix this by manually cross-referencing the dataset's calibration files and the drone's CAD documentation, realizing that I needed to trust the hard data over the AI's "estimated" rotations.
Section 3: Engineering Validation
To ensure the reliability of my Visual SLAM output, I employed a benchmark-driven validation strategy. Since my goal was to achieve a high score on the leaderboard (eventually reaching 92.6), I could not rely on AI-suggested "rules of thumb" for parameter tuning.
I validated my configurations through three main methods:
Benchmark Iteration: I systematically increased the number of ORB features from the default to a "crazily high" 15,000. I validated this by running the evaluation script evaluate_vo_accuracy.py after each change to ensure the RMSE (Root Mean Square Error) was decreasing without causing the VM to crash due to memory overhead.
Reproducibility Testing: I documented my specific steps for the ARM environment in a markdown file to ensure another user could replicate my results. I cross-checked my output format against the standard Windows/Docker output to ensure compatibility within our team's integrated pipeline.
Ground Truth Comparison: I visually compared the generated trajectories against the AMtown02 scene layout to ensure that the "stationary" parts of the flight (where VO typically fails) were being handled as gracefully as possible.
Section 4: Problem-Solving Process
The most significant technical challenge I faced was the "CXX Error" during the compilation of ORB-SLAM3 on my Apple Silicon Mac. As a self-taught coder, I initially didn't understand that "CXX" was simply shorthand for C++. The build process kept failing with obscure compatibility messages regarding the compiler flags.
My reasoning process followed these steps:
Initial Diagnosis: I first asked Gemini to explain the CXX error. While it gave me general information about compiler versions, it didn't solve the specific ARM-architecture conflict.
Research & Pivot: I realized that "Googling for solutions" was only getting me part of the way. I had to learn how Linux handles architecture-specific libraries. I spent days on Stack Overflow and GitHub issues (specifically the ORB-SLAM3 and ROS Noetic issues for ARM64).
Manual Integration: I found that certain third-party libraries (like Pangolin or OpenCV) needed to be built from source with specific flags to work within a UTM virtual machine. The AI actually failed here because it kept suggesting standard apt-get commands that were incompatible with my specific virtualization layer.
Resolution: I manually edited the CMakeLists files to force the correct compiler paths. This taught me that self-learning and "developer instinct" are more impactful than being "spoon-fed" solutions by an AI or a pre-defined course workflow.
Section 5: Learning Growth
This course has been transformative. At the start, my confidence in setting up complex robotics environments was low (1/5). I viewed tools like Docker as a black box. By the end, I reached a 5/5 confidence level in tooling readiness.
The skills that improved the most were my understanding of Linux system architecture and the mechanics of Visual SLAM. I can now independently build complex C++ projects from source, manage dependencies in a virtualized environment, and tune SLAM hyperparameters based on real-world drift patterns. What felt impossible—running high-performance SLAM on a non-native, ARM-based Mac—now feels manageable. I have moved from being a "consumer" who follows instructions to an "architect" who creates custom engineering environments.
Section 6: Critical Reflection
AI played a balanced role in my journey. It acted as a "force multiplier" for documentation and drafting—for example, it helped me generate the initial structure for my group presentation slides, allowing me to focus on the technical content. However, I learned not to trust it for 100% of my problems.
I relied on AI appropriately for "boilerplate" tasks and definitions, but I learned that relying on it for high-stakes technical decisions (like IMU calibration) can be dangerous. If I were to do this project again, I would spend more time learning the fundamental Linux system calls earlier on, rather than initially "Googling for solutions" as a first resort. I’ve realized that the most satisfying parts of the project were the ones I solved myself through trial and error, such as the CXX error resolution.
Section 7: Evidence
7.1 Code Snippets
AI-suggested IMU refinement (Misleading):
# Gemini suggested this to "fix" the leftward drift
Tbc: !!opencv-matrix
   data: [ -0.008726, -0.999924,  0.008727, -0.00674,
            0.008727, -0.008726, -0.999924, -0.02840,
            0.999924,  0.008650,  0.008803, -0.09965,
            0.0,       0.0,       0.0,       1.0]


Note: This increased drift because the rotation matrix was not aligned with the actual drone CAD orientation.
7.2 Prompt Examples
Prompt: "Create a Google Slides for my presentation of 5-6 minutes regarding the refinements of visual odometry... and point out I used Apple Silicon via UTM rather than Docker."
Result: Helpful for structure, but failed to include the specific technical figures (intrinsic/extrinsic parameters) required for a professional engineering talk.
7.3 Accuracy Evidence
Leaderboard Achievement: Successfully reached a score of 92.6 by scaling ORBextractor.nFeatures to 15,000, validating that a high-density feature approach can compensate for certain environment-related drifting in the AMtown02 dataset.
