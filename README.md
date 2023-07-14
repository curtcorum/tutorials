# Welcome to the Pulseq tutorials repository!

This is a collection of various Pulseq tutorials, which is supposed to grow in future. If you don't know where to start, read through  this document and study the [Pulseq Concepts](./doc/pulseq_Concepts_Nov2022.pdf) presentation. Thereafter work through the tutorials one-by-one. Please let us know if your favorite topic is missing or needs more attention, we will do our best to cover it in future.

# Foreword

The *Pulseq*[^1] software provides an open-source framework for the
development, analysis, display, and execution of magnetic resonance (MR)
sequences. To familiarize yourself with the Pulseq software, we
recommend starting your Pulseq journey with this *Pulseq Tutorial*. It
was initially developed for the Pulseq software demonstration and
hands-on session at the Italian Chapter of ISMRM 2022 in Pisa and few
other live demos. This Tutorial contains example pulse sequences, raw MR
data in the Siemens TWIX format, and reconstruction scripts, ranging
from a free induction decay (FID) sequence to non-Cartesian radial and
spiral sequences, in the order of increasing complexity. This document
provides detailed instructions on how to work through the Tutorial.
After going through this guided tour, you will probably appreciate both
the simplicity and the power of Pulseq and be able to move on to
building your own sequences with Pulseq!

## Naming convention of the tutorials in the collection
The tutorials are ordered by increasing complexity, so if unsure where to start, 01_... is a good option.

In more detail, the naming convention is

`LN_Topic`

where 

* **L** is a one-digit level going from 0 for *beginner* to 1 for *intermediate* to 2 *advanced*;
* **N** is a sequential single-digit number (this assumes we can only have up to 9 tutorials per complexity level);
* **Topic** is a free text briefly describing the scope of the tutorial.

If you do not know where to start, the [01_from_FID_to_PRESS](01_from_FID_to_PRESS) would be
a good option, which describes how to code sequences starting from a most basic FID pulse-acquire sequence moving on towards a reasonably optimised point-resolved spectroscopy (PRESS) sequence.

# Brief Pulseq Tutorial Instructions

by Qingping Chen 

*Division of Medical Physics, Department of Radiology,
University Medical Center Freiburg, Germany*

Created on June 21 2023

If you come across any problems, please contact our pulseq
team: pulseq.mr-at-uniklinik-freiburg.de

# Contents 

[**Foreword**](#foreword)

[**1. Prerequisites**](#1-prerequisites)

[1.1 MR Physics Background](#11-mr-physics-background)

[1.2 Programming Tool](#12-programming-tools)

[1.3 Pulseq Software](#13-pulseq-software)

[1.4 mapVBVD Software](#14-mapvbvd-software)

[1.5 Text Compare Tool](#15-text-compare-tool)

[**2. Introduction to Pulseq**](#2-introduction-to-pulseq)

[2.1 Matlab and Pulseq Sequence Files](#21-matlab-and-pulseq-sequence-files)

[2.2 Pulseq Interpreter for Siemens scanner](#22-pulseq-interpreter-for-siemens-scanner)

[2.3 Blocks and Events in Pulseq](#23-blocks-and-events-in-pulseq)

[**3. Pulseq tutorials**](#3-pulseq-tutorials)

[3.1 01\_from\_FID\_to\_PRESS](#31-01-from_fid_to_press)

[3.2 02\_basic\_gradient\_echo](#basic_gradient_echo)

[3.3 11\_from\_GRE\_to\_EPI](#from_gre_to_epi)

[3.4 12\_Radial\_and\_nonCartesian](#radial_and_noncartesian)

# 1. Prerequisites

## 1.1 MR Physics Background

To be able to code MR sequences, knowledge in MR physics is required. We
recommend some useful books and websites listed below.

**Books:**

-   Brown et al. Magnetic Resonance Imaging: Physical Principles and
    Sequence Design, 2014.
    (<https://onlinelibrary.wiley.com/doi/book/10.1002/9781118633953>)

-   Nishimura, Principles of Magnetic Resonance Imaging, 2010.
    (<https://books.google.de/books/about/Principles_of_Magnetic_Resonance_Imaging.html?id=uz9BAQAAIAAJ&redir_esc=y>)

**Websites:**

-   Common questions and answers in MRI: <http://mriquestions.com/>.

-   MR Bloch Simulator:
    [www.drcmr.dk/BlochSimulator](http://www.drcmr.dk/BlochSimulator).

## 1.2 Programming Tools

-   Matlab software (https://mathworks.com/products/matlab.html) needs
    to be installed in your computer.

-   Basic familiarity with Matlab programming is required.

## 1.3 Pulseq Software

Please download the Pulseq software from
<https://github.com/pulseq/pulseq>. Install Pulseq software in Matlab by
adding its directory and subdirectories to Matlab's path.

## 1.4 mapVBVD Software

The mapVBVD software is required to read raw data in Siemens TWIX
format. The software can be download from\
<https://github.com/CIC-methods/FID-A/tree/master/inputOutput/mapVBVD>\
and installed by adding its directory to Matlab's path.

## 1.5 Text Compare Tool

We recommend using a text comparison tool to compare the sequences
within the subsequent steps to visualise the changes that occur at each
step. *Meld* software can be used for the text comparison and can be
downloaded from <https://meldmerge.org/>.

# 2. Introduction to Pulseq

Pulseq is an open-source framework for the development, analysis,
display, and execution of MR sequences for imaging and spectroscopy.
Under this framework, one can directly program an MR sequence in Matlab
or Python and execute the Pulseq sequence file on real scanner hardware
through the corresponding Pulseq interpreter. A central contribution of
Pulseq is that it provides an open file format to compactly describe MR
pulse sequences suitable for execution on a real MR scanner. The
detailed *file specification* can be obtained from
<https://pulseq.github.io/specification.pdf>.

## 2.1 Matlab and Pulseq Sequence Files

For generation of *Pulseq sequence files* that are readable by an MR
scanner, we run Matlab scripts (extension: **.m**), e.g. s01\_FID.m.
When a Matlab script runs successfully, a Pulseq sequence file is
created (extension: **.seq**), e.g. s01\_FID.seq. This file defines all
sequence timing and events, including RF, delays, trapezoid gradients,
arbitrary gradients, and ADC events. This forms a complete description
of the sequence, and is ideal for rapid prototyping and executing
sequences programmed in alternative environments (e.g. JEMRIS). This
text file can be read by the Pulseq interpreter on the scanner and
stored as compressed sequence blocks in memory. Matlab and Python
toolboxes also provide functionality to read Pulseq .seq files.

# 2.2 Pulseq Interpreter for Siemens scanner

The Pulseq interpreter can be used to execute the Pulseq sequence file
(.seq file) generated by Matlab, PyPulseq or other tools on a particular
MR scanner. The interpreter for Siemens scanners can be obtained after
signing a Siemens C2P agreement (for more information, please send an
inquiry to pulseq.mr-at-uniklinik-freiburg.de). The source code contained
in the interpreter package can be compiled in the Siemen IDEA environment, and
then the .so and .dll files are produced. Copy both binary files to
the sequence folder on the MR scanner (folder defined by the
`%CustomerSeq%` variable, `C:\MedCom\MriCustomer\seq` for Numaris4
`C:\ProgramData\Siemens\Numaris\MriCustomer\CustomerSeq` for
NumarisX). These binary files of the Pulseq interpreter sequence are
universal and can be used for any .seq, so they only need to be copied
to the scanner once.

The .seq file that you want to execute need to be
placed in the subfolder `%CustomerSeq%\pulseq`. By default the file
named `external.seq` is used. Important: for the default protocol
corresponding to the Pulseq interpreter sequence a valid .seq file need
to be placed in `%CustomerSeq%\pulseq` stored under a name
`external.seq`. To access the Pulseq interpreter sequence on the MR
scanner, you need to create the corresponding protocol and eventually
store it in your favourite examination folder. Navigate to Dot Cockpit
Program Editor Browse Default Sequence Region Customer sequences
Default. Now insert the pulseq sequence into any protocol. Run this
protocol and it will automatically read the pulseq file `external.seq`
in the folder `%CustomerSeq%\pulseq`. Refer to the Pulseq C2P documentation for
further details.

# 2.3 Blocks and Events in Pulseq

Under the specification of the Pulseq file format, a sequence is
established based on at least one *block* (`id_block>0`). Each
entry in the block table declares a single block specified by a duration
(DUR) in the units of BlockDurationRaster and a list of six event IDs:

    <id_block> <dur> <rf_id> <gx_id> <gy_id> <gz_id> <adc_id> <ext_id>

The six events include an RF event (RF), three gradient events (GX, GY,
GZ), an ADC event (ADC), and an extension event (EXT). An event ID of 0
indicates no event.

The RF events are declared in a section with the `[RF]` keyword. Each
line in the section is specified by eight numbers:

    <rf_id> <amp> <mag_id> <phase_id> <time_id> <delay> <freq_offset> <phase_offset>

The GRADIENT events are declared in two sections. Arbitrary gradients
are located in the section marked with the `[GRADIENTS]` keyword. Each line in
the section is specified by five numbers:

    <grad_id> <amp> <shape_id> <time_id> <delay>

Trapezoidal gradients are located in the section labeled with the `[TRAP]`
keyword. Each line in this section is specified by six numbers:

    <grad_id> <amp> <rise> <flat> <fall> <delay>

As you may have noticed, arbitrary gradients and trapezoidal gradients 
share the ID space, that is `grad_id` uniquely identifies every gradient, 
with each ID only allowed to be present either in the `[GRADIENTS]` 
section or in the `[TRAP]` section.

The ADC section is declared with the `[ADC]` keyword. Each line in this
section is specified by six numbers:

    <adc_id> <num> <dwell> <delay> <freq_offset> <phase_offset>

The EXTENSION section is declared with the `[EXTENSIONS]` keyword. Each
line in the section is specified by four numbers:

    <ext_id> <type> <ref> <next>

Take one of the simplest MR experiments -- free induction decay (FID) as
an example. The FID sequence contains two blocks:

    [BLOCKS]
    1 62 1 0 0 0 0 0
    2 499947 0 0 0 0 1 0

The first block has a duration of 62 \* *BlockDurationRaster* us and contains
an RF event with an ID of 1. The block has no gradients or ADC events,
indicated by zero IDs and also no extension is specified. The RF event
is declared by a line:

    [RF]
    1 500 1 2 3 100 0 0

The RF event has a peak amplitude of 500 Hz and a delay of 100 us before
starting the RF pulse. The magnitude, phase, and time compressed shapes
are defined with the shape IDs of 1, 2, and 3, respectively. The RF
pulse has no frequency or phase offsets.

The second block has a duration of 499947 \* *BlockDurationRaster* us and
contains an ADC event with an ID of 1, declared by:

    [ADC]
    1 8192 31250 29730 0 0

The ADC event contains an ADC directive with 8192 samples, a dwell time of 31250
ns, and a delay of 29730 us between the start of the block and first
sample, and no frequency and phase offsets.

For more detailed specification of blocks and events, please refer to
the Pulseq specification documentation at
<https://pulseq.github.io/specification.pdf>.

# 3. Pulseq tutorials

This section introduces the tutorial topics one-by-one in details.

## 3.1 01\_from\_FID\_to\_PRESS

This tutorial introduces a step by step the design of a PRESS sequence
starting from a very basic FID sequence. See [tutorials/01_from_FID_to_PRESS](https://github.com/pulseq/tutorials/tree/main/01_from_FID_to_PRESS)

## 3.2 02\_basic\_gradient\_echo

This tutorial demonstrates how to expand a basic Gradient-Echo (GRE)
sequence to an advanced GRE sequence with a desired steady-state
magnetisation evolution needed to generate T1 contrast. It contains six
steps from ***s01\_GRE\_tutorial\_step0*** to
***s06\_GRE\_tutorial\_step5***. We recommend using Meld software to
highlight the changes at each step. The slide titled
***02\_basic\_gradient\_echo.pptx*** shows the sequence diagrams of all
steps and visualises the changes at each step.

The transverse magnetisation may persist from cycle to cycle in a GRE
sequence with short repetition times (e.g. shorter than \~3\*T2).
*Spoiling* tries to approximate a situation that the steady-state
magnetisation has no transverse components immediately before each RF
pulse. Three methods may be used alone or in combination to spoil
transverse magnetisation.

1.  Long TR spoiling. When TR\>\>T2\*, the transverse magnetisation will
    naturally decay to zero by the end of the cycle. Thus, any GRE
    sequence using TR values of several hundred milliseconds or longer
    will be "naturally" spoiled.

2.  Gradient spoiling. In this method, spoiling is performed by applying
    the slice-selective (and sometimes readout) gradients at the end of
    each cycle just before the next RF pulse. Several gradient spoiling
    concepts have been tried in the MR history, with the strength of the
    spoiler gradient remaining constant or varied linearly or
    semi-randomly from TR to TR. In the present example a constant
    spoiler is used and an appropriate phantom with a sufficiently long
    T2 it demonstrates that gradient spoiling does not work unless the
    spoiler is so strong that the intrinsic diffusion weighting would
    kill the signal.

3.  It is also demonstrated that it is necessary to refocus the phase
    encoding moment at the end of the TR cycle to avoid artefacts.

4.  RF-spoiling. Here the phase of the RF carrier is changed according
    to a predefined formula from TR to TR. Using a completely randomised
    pattern of phase changes is not ideal because unintended spin
    clustering may occur, and the degree of spoiling may change from one
    interval to the next. A superior method is to increment the phase
    quadratically using a recursive formula. RF spoiling is always
    combined with a moderate constant spoiling in read and slice
    directions and phase-encoding refocusing. For further details about
    spoiling, please go to
    <https://mriquestions.com/spoiling---what-and-how.html>.

***s01\_GRE\_tutorial\_step0***

***s01*** is a *basic* 2D slice-selective GRE sequence. Each TR of this
sequence contains 5 blocks. The corresponding k-space is shown in Figure
2.

![](media/image3.png)

**Figure** **2** K-space of s01\_GRE\_tutorial\_step0 sequence (6\*6
encodes).

***s02\_GRE\_tutorial\_step1***

***s02*** is a 2D slice-selective GRE sequence with three spoiler
gradients added in slice-selective, readout and phase-encoding
direction. Its k-space is shown in Figure 3.

![](media/image4.png)

**Figure** **3** K-space of s02\_GRE\_tutorial\_step1 sequence (6\*6
encodes).

***s03\_GRE\_tutorial\_step2***

***s03*** is a 2D slice-selective GRE sequence with two spoiler
gradients in slice-selective and readout directions and one rewinder
gradient in the phase-encoding direction. It is built by altering the
gyPost gradient in ***s01*** to rephase in the phase-encoding direction
for subsequent phase-encoding steps. The k-space is shown in Figure 4.

![](media/image5.png)

**Figure** **4** K-space of s03\_GRE\_tutorial\_step2 sequence (6\*6
encodes).

***s04\_GRE\_tutorial\_step3***

***s04*** is built by adding RF-spoiling to ***s03***. The RF-spoiling
is achieved by quasi-randomly varying the RF phase offset in the *i*^th^
phase-encoding step,
$\text{φ}_{\text{i}}\text{\ =\ mod}\left( \text{117\ *\ }\left( \text{i}^{\text{2}}\text{\ +\ }\text{i}\text{\ }\text{+\ 2} \right)\text{,\ 360} \right)\text{*}\frac{\text{π}}{\text{180}}$.

***s05\_GRE\_tutorial\_step4***

In ***s05***, the receiver phase offset is set to follow the transmitter
phase offset, $\text{φ}_{\text{i}}$, in the *i*^th^ phase-encoding step.

***s06\_GRE\_tutorial\_step5***

***s06*** adds some *dummy* scans (i.e. plays out multiple cycles of the
sequence without recording a signal) to ***s05*** to establish (near)
steady-state magnetisation in the GRE sequence prior to beginning of the
ADC recording. For more details about dummy scans, please go to
<https://mriquestions.com/dummy-cycles.html>.

## 3.3 11\_from\_GRE\_to\_EPI

This topic introduces the way to establishm an Echo-Planar Imaging (EPI)
sequence based on a GRE sequence. The slide titled
***11\_from\_GRE\_to\_EPI.pptx*** shows the sequence diagrams of all
steps and visualises the changes at each step.

***s01\_GradientEcho***

***s01*** is a single-echo GRE sequence with Ny\*numTE phase encodes.
Its echo time (TE) cycles by the defined TEs (\[4 9 15\] \* 1e-3). numTE
is the number of the defined TEs. Note: the mr.align function is very
useful to set delays of events within a block to achieve desirable
alignment.

***s02\_MultiGradientEcho***

***s02*** is a monopolar multi-echo GRE sequence. The timing is
calculated with the aid of helperT. gxFlyBack gradient is used to
rephase the readout gradient.

***s03\_BipolarMultiGradientEcho***

***s03*** is a bipolar multi-echo GRE sequence. It eliminates gxFlyBack
gradients by reversing the polarity of the even readouts.

***s04a\_SegmentedGradientEcho***

***s04a*** is a readout-segmented single-echo GRE sequence. Instead of
sampling the k-space for one readout during each TR, it samples the
k-space with multiple readouts during each TR (i.e. segmented readouts).
Bipolar readout gradients are used to avoid time loss due to additional
fly-back gradients. The area of phase-encoding (PE) gradients
(pre-dephasing and blip) is determined based on the number of segmented
readouts (nSeg).

***s04b\_SegmentedGradientEcho***

In s04b, the splitGradientAt function is used to split the gyBlip
gradient of ***s04a*** into two parts, each in a separate block, which
reduces the time interval between two adjacent segmented readouts. In
the first segmented readout block, the first part of gyBlip is added
after the readout gradient. In inner segmented readout blocks, the
second and first parts of gyBlip are combined and added before and after
the readout gradient. In the last segmented readout block, the second
part of gyBlip is added before the readout gradient.

In case the gyBlip.riseTime is shorter than the fallTime of the former
block, gyBlip.delay is increased, and thus the peak of gyBlip is moved
to the edge of the former block. In case gyBlip.riseTime is longer than
the fallTime of the former block, a delay is added to the later readout
gradient, such that the last point of gyBlip hits the last point of the
ramp-up of the later readout gradient.

***s04c\_SegmentedGradientEcho***

***s04c*** increases the nSeg of ***s04b*** from 5 to Ny, such that
***s04c*** traverses the whole k-space in one segmented scan (i.e. an
improvised EPI scan).

***s05\_EchoPlanarImaging***

***s05*** is a 2D EPI sequence with the shortest timing. It contains
Ny+3 blocks.

The mr.makeDigitalOutputPulse function defines the output trigger to
play out with every slice excitation.

The shortest timing is achieved by using maximum slew rate and ramp
sampling. gyBlip is split into two parts and distributed to two adjacent
blocks. The dead times of the readout trapezoid gradient are at the
beginning and the end (align with two parts of gyBlip), each equal to
half of gyBlip.Duration. The readout gradient is first calculated based
on the maximum slew rate (with consideration of the dead times). Then,
its amplitude is scaled down to fix the area to be Nx\*deltak.

The ADC dwell time on the readout flat-top is calculated based on
adcDwellNyquist=deltak/gx.amplitude. The dwell time is rounded down to
system.adcRasterTime. Note that the number of ADC samples on Siemens
should be divisible by 4. In addition, the ADC should be aligned with
respect to the readout gradient: *both Pulseq and Siemens define the ADC
samples to happen in the centre of the dwell period*.

***s06\_EPI\_SingleTraj***

***s06*** is a 2D EPI sequence constructed from a single arbitrary
trajectory. It is based on ***s05*** and contains 4 blocks.

A dummy sequence object (seq\_d) creates and exports the single EPI
trajectory in xy-plane with Ny phase encoding steps. A single ADC object
is created to sample from the start to the end of the single trajectory
(excluding the ADC dead times:
adcDur=seq\_d.duration-2\*sys.adcDeadTime). A real sequence object
(seq\_r) is created to combine the slice-selective excitation, Gz
rephasing and Gx and Gy dephasing, the single EPI trajectory and the
single ADC, and Gz spoiler together, for a total of 4 blocks.

## 3.4 12\_Radial\_and\_nonCartesian

This tutorial introduces the steps necessary for developing
non-Cartesian sequences (radial and spiral) based on Cartesian
sequences. The slide titled ***12\_Radial\_and\_nonCartesian.pptx***
shows the sequence diagrams of all steps and visualises the changes at
each step.

***s01\_CartesianSE***

***s01*** describes a 2D slice-selective SE sequence with 7 blocks in
each TR. Note that the polarity of the gy gradient in deltaTR (that is
the polarity of both phase encoding and rephrasing gradients) remains
unchanged due to the 180° refocusing RF pulse.

***s02\_RadialSE***

***s02*** describes a 2D slice-selective radial sequence, built based on
***s01*** using the mr.rotate function. The mr.rotate function rotates
the readout gradient and its pre-phaser around the *z*-axis to a certain
angle, which projects the gradient into the *x*- and *y-*axis and thus
produces two components.

***s03\_CartesianGradientEcho***

***s03*** describes a 2D slice-selective Cartesian GRE sequence, similar
as ***s01*** from the Tutorial 11\_from\_GRE\_to\_EPI.

***s04\_RadialGradientEcho***

***s04*** describes a 2D slice-selective radial GRE sequence built based
on ***s03***.

***s05\_FastRadialGradientEcho***

***s05*** describes a 2D slice-selective radial GRE sequence with the
shortest timing. The mr.addGradients function combines slice-selective
gradient and slice-refocusing gradient into a single "ExtendedTrapezoid"
gradient. The mr.align function is used to position the gxPre gradient
right after the RF pulse and before the end of the slice-refocusing
gradient. The flat time of the readout gradient is extended for
spoiling. The whole sequence has a total of 2 blocks per TR.

***s06\_Spiral***

***s06*** describes a 2D slice-selective spiral sequence with fat
saturation. A Gaussian RF pulse followed by a spoiling gradient in the
*z*-axis is used for fat saturation. A raw Archimedean spiral is
generated and then resampled to stay directly under the slew rate and
maximum gradient limits. The mr.traj2grad function calculates gradient
strength and slew rate based on k-space trajectory. The
mr.makeArbitraryGrad function generates an arbitrary gradient (e.g.
spiral).

[^1]: Layton KJ, Kroboth S, Jia F, et al. Pulseq: A rapid and
    hardware-independent pulse sequence prototyping framework. *Magn
    Reson Med*. 2017;77(4):1544-1552. doi:10.1002/mrm.26235
