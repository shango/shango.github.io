---
title: Multi-channel EXR files in Adobe After Effects 2005
layout: page
parent: UI Components
nav_order: 2
---

# Using Multi-channel EXR files in Adobe After Effects 2005 


## Introduction 

This guide provides a quick-start workflow for integrating multi-channel 32-bit EXR files into Adobe After Effects using an Academy Color Encoding System (ACES) pipeline. It explains how to configure After Effects for ACES color management, interpret EXR footage correctly, composite in a 32-bit linear color space, and export while preserving color fidelity.

---

## Who Is This Guide For?

This guide is designed for VFX artists and compositors who need to maintain color accuracy when working with EXR files in After Effects as part of an ACES-compliant workflow.

The workflow is intended for use in professional visual effects pipelines and assumes a working knowledge of After Effects and basic familiarity with EXR files.

---

## What This Guide Covers

This guide steps you through implementing a color-managed workflow in After Effects using multi-channel EXR files.

By the end, you’ll be able to:
- Import and separate multi-channel EXRs.
- Apply ACES color transforms using OCIO.
- Composite in 32-bit linear space with confidence.
- Export EXRs or video while preserving color accuracy.

---

## What Are 32-bit Multi-channel EXR Files?

32-bit multi-channel EXR files are the industry standard for high-dynamic-range scene-linear image data. They support multiple render passes in a single file, 32-bit floating-point color, and lossless compression.

For more information, see the [OpenEXR documentation](https://openexr.com/en/latest/).
### The After Effects built-in OCIO color engine

After Effects includes a built-in OpenColorIO (OCIO) color engine, which provides a complete color management solution for motion picture workflows.

For an in-depth overview, visit [OpenColorIO](https://opencolorio.org/).

OCIO will use the ACES to handle all color transformations to do the heavy lifting of keeping color spaces consistent for you from file import, through compositing and on to file rendering.

ACES is a standardized color management framework designed to ensure consistency across digital imaging workflows. It enables accurate color interchange, supports complex compositing pipelines, and facilitates the creation of high-fidelity masters for distribution and archival.

To dive deeper into ACES, visit the [ACES Central website](https://acescentral.com/).

---

## Setting Up the After Effects Project

To enable an ACES workflow in After Effects, you must first disable Adobe’s native color management and use OCIO color management (see Figure 1).

1. Navigate to **Edit > Project Settings**.

2. In the Project Settings window, select the **Color** tab.

3. In the **Color Management** section, set the following:

	1. Color Engine to **OCIO color managed**.

	2. OCIO Configuration to **ACES 1.2**.

	3. Bit Depth to **32 bits per channel (float)**.
   
4. Under **Working Color Space**, select:  

   **compositing_linear: ACES - ACEScg**
   (This sets the compositor to ACES version of linear light for the project)

5. In the **Display Color Space** section, choose either **ACES/sRGB** or **ACES/Rec.709** depending on your monitor's color profile.

![Project Settings color tab](/images/proj_settings_color_tab.png)

Figure 1. Project Settings dialog box - Color tab

---

## Importing Your Footage

To import your EXR sequence, navigate to **File > Import > File…**.

In the Import dialog (see Figure 2), select the first frame of your EXR sequence. Then, in the **Sequence Options** section, select: **OpenEXR Sequence**

Next, under **Import As**, select: **Composition – Retain Layer Sizes**

Click **Import** to bring the sequence into your After Effects project.

![Import dialog](/images/import_dialogue_01.png)

Figure 2. Import File dialogue box

After selecting your files and clicking **Import**, you'll see the **Import Options** dialog (see Figure 3). A recommended workflow is to import the footage as a composition, automatically pre-compose the layers, and generate a contact sheet—a composition that displays all layers from the EXR sequence for easy preview.

In the **Import Options** dialog box select: 

1. Import As: **Composition**
2. **Pre-compose Layers**
3. **Create Contact Sheet**

![Import dialog 2](/images/import_dialogue_02.png)

Figure 3. Import Options Dialogue Box

---

## Footage Interpretation

Once your source footage is imported, you need to apply the correct input transform to each clip (see Figure 4).

Steps to correctly interpret footage:

1. Right-click the footage in the Project panel and choose **Interpret Footage**.

2. In the **Interpret Footage** dialog, check **Show All** next to the *Media Color Space* dropdown.

3. From the dropdown, select the correct media color space based on the footage’s origin (e.g., **Alexa LogC**, **sRGB**, **Rec.709** etc.).

4. Repeat for all source clips.

*Tip: For 8- or 16-bit files with unknown color profiles, you may need to experiment. Try **ACES/sRGB** or **ACES/Rec.709** and select whichever gives the most accurate result.*

![Interpret footage](/images/interpret_footage.png)

Figure 4. Interpret Footage Dialogue Box - Color tab


## Working with EXtractoR

The **EXtractoR** plugin allows control of which layers and channels are displayed (see Figure 6).

Each pre-composed layer will use the After Effects **EXtractoR** plugin to isolate individual channels and layers from the EXR file.

For an in-depth overview, visit this document [Adobe After Effects, Included Third Party Effects](https://helpx.adobe.com/after-effects/using/3d-channel-effects.html)

![ExtractoR plugin](/images/Extractor_plugin.png)

Figure 6. Effects panel view - EXtractoR plugin.


**Double click** the Contact Sheet composition in your Project Pane.  In the Project Viewport, you will see a thumbnail representation of each layer in your EXR Files (see Figure 5). 

![Contact sheet](/images/contact_sheet.png)

Figure 5. Contact sheet composition view

---

## Exporting with the Correct Color Space

To preserve color accuracy during delivery, configure your output settings to match the desired color space.

In the Output Module settings, go to the **Color** tab and select the appropriate output color space to match your project or delivery format (see Figure 7).

**Steps:**

1. Add your composition to the **Render Queue**.

2. In the **Render Queue** panel, click on the **Output Module** settings.

3. In the **Output Color Space** dropdown, select the appropriate color space.  

   This is typically the same as the original input footage or required delivery format.

4. Start the render.

![Output module](/images/output_module.png)

Figure 7. Output module - color tab

---

## Final Notes

- Always verify color settings at every stage of the pipeline—input, composition, and export.

- Consider using consistent naming conventions for exported files to indicate color space (e.g., `Scene01_shotA_ACEScg.exr`).

- Ensure that all departments in your pipeline use the same OCIO configuration to maintain color consistency.



Version 1.0 – April 2025