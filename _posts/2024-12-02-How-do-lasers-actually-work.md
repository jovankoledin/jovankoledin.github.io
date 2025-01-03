## How do lasers actually work?
 
As an Embedded Software Engineer who writes code that controls lasers/directed energy systems there were many things about them that I didnt know when I started.
You might know that lasers are made by running current through a laser diode and that laser diode creates the laser beam. But there is a lot more information about a laser that can be used to understand and control them. How do we actually control lasers? What is laser polarization? What is the phase of a laser? How do we convert a laser from the optical domain to the signal domain and plot a laser on an Oscilloscope? Below I will attempt to answer all of these questions and give 
you a better understanding of a really cool technology.

![Alt text](/images/Laser/Figure1.png)  
Figure 1. Lockheed Martin IFPC-HEL laser program that I worked on [[1]](https://breakingdefense.com/2023/07/lockheed-secures-221m-army-deal-for-high-powered-air-defense-laser-prototype/)

### How do we Control a Laser?
 
There are a few different ways in which we can control/modulate a laser. The first is Intensity Modulation, this is where the laser's power/intensity
is varied over time. This could be done by turning the laser on and off rapidly. Another would be Frequency Modulation, this is where the laser's
wavelength or frequency is varied over time. You might be asking how a laser has a frequency, isn't it just a beam of energy? Not quite, a laser 
is really a light wave that contains a lot of energy, so the frequency of a laser is essentially just the wavelength of the underlying light wave
for our laser. Remember that different colors of light have different wavelengths or frequencies as well, so shifting the frequency of our laser 
would be similar to changing the color of a beam of light. The next type of laser control is Phase Modulation, this is where the phase of the laser 
light is varied over time, think of shifting a signal in the time domain, one of the main differences between a laser diode and a light bulb is that
a laser diode emits a light beam which is coherent or aligned in phase. A light bulb is not coherent because of the curvature of the light bulb, this
causes the light wave to be emitted and dispersed from different points along the light bulb. Some light beams get emitted first, like the ones where
the bulb is closer to the filament, i.e. the neck of the bulb. But when the bulb is further away from the filament then the light waves won't emit 
until later, these waves would be behind in phase from the light waves that were emitted from the neck of the bulb. The next type of laser control 
is Amplitude Modulation, this is where the laser output follows a sinusoidal or periodic pattern applied to a laser. The next type would be Pulse 
modulation. This is where a laser is modulated to produce pulses of light with very short durations, this is what is done in high-precision cutting, 
welding, laser-eye surgery and time-of-flight measurements. All of these types of modulation are ways in which we can control a laser's  output.
 
### What is Laser Polarization?
 
Lasers emit electric fields, polarization refers to the direction of the electric field oscillation for a laser. There are different types of polarization,
linear polarization is where the electric field oscillates in a single plane. The next type is circular polarization, this is where the electric field 
rotates in a clockwise or counterclockwise direction. The last type would be Elliptical polarization, this is where the electric field traces the shape 
of an ellipse. Why do we want to control the electric field/polarization of a laser? There are many reasons, most optical devices (polarizers, waveplates,
and diffraction gratings) are polarization sensitive. Some optical systems like fiber optics can greatly affect the polarization of a laser. When you bend
a fiber cable or change its temperature you introduce something called birefringence which causes the polarization state to change along the fiber. 
The Polarization of your laser is an incredibly important factor to keep in mind, in Optical Communication Random changes in polarization due to fiber
birefringence can cause power fluctuations in polarization-dependent devices (e.g., modulators, multiplexers). In sensing applications Polarization changes 
affect measurements in fiber-optic sensors used for temperature, strain, or pressure monitoring. Quantum Optics and Coherent Communication also rely on
specific polarization states for encoding or decoding data and require precise polarization control. To control the polarization of a laser in the fiber 
cables your laser is propagating through there are several techniques you can use: Polarization Controllers are devices inserted into the system dynamically
to adjust polarization. Polarization Maintaining fibers are used when stable polarization states are needed. Polarization Scramblers intentionally randomize
the polarization state to average out polarization-dependent effects. Active Feedback Systems provide real-time monitoring and adjustment to ensure consistent
polarization alignment.

![Alt text](/images/Laser/Figure2.png)  
Figure 2. Polarization of a laser visualized with its electric field [[2]](https://byjus.com/physics/polarization-of-light/)

### How do we convert a Laser to a signal domain suitable for viewing it on a Oscilloscope?
 
Let's say we want to ensure that the amplitude and frequency of our laser is up to spec for our system. The first thing we would want to do is figure out a way
to find a way to convert its optical signal into an electrical signal that an oscilloscope can process. The most common way to do this is to use a photodetector
like a photodiode or phototransistor. They translate the optical power of our laser into a corresponding electrical signal. Following are the typical steps that
are usually taken to do this. The first step is to select an appropriate photodetector, usually it is a safe pick to use a photodiode detector to measure a laser 
signal in the visible and near-infrared spectrum, you want to pick a photodiode that matches your laser's wavelength, you should make sure that the detector can
handle the laser's power level and frequency. Then you just need to align the laser with the photodetector by using free space optical mounts and then focusing 
the laser onto the active area of the photodetector to maximize the signal. Next you need to connect the photodetector or amplifier if using one to the oscilloscope
input and ensure the impedance of the Oscilloscope matches the circuit (usually 50 Ohms or high impedance). Make sure you are trying to minimize the electrical 
noise produced by the laser system, the best way to do this is to use shielded cables that are properly grounded to carry power and signals through your system 
that are most likely high power and generating high noise.

![Alt text](/images/Laser/Figure3.png)  
Figure 3. Sine wave that might look like a laser displayed on a Oscilliscope [[3]](https://learn.sparkfun.com/tutorials/how-to-use-an-oscilloscope/all)

### Sources
1. https://breakingdefense.com/2023/07/lockheed-secures-221m-army-deal-for-high-powered-air-defense-laser-prototype/
2. https://byjus.com/physics/polarization-of-light/
3. https://learn.sparkfun.com/tutorials/how-to-use-an-oscilloscope/all

