# Unity-Impact-Frame-System
Unity visual effects system that creates "impact frame" moments during gameplay.

## Overview

The Impact Frame System creates visual effects at key moments in your game, such as successful hits, critical strikes, or impactful events. 

## Features

### Visual Effects
- **Edge Detection**: Multi-method edge detection using depth, normals, and luminance
- **Radial Blur**: Dynamic blur emanating from the impact point
- **Animated Lines**: Moving noise-based line animation with customizable speed
- **Blast Sphere Effect**: 3D world-space expanding sphere visualization
- **Pulsing Animation**: Rhythmic pulsing effect on detected edges
- **Customizable Colors**: Full control over edge and background colors

### Technical Features
- **URP Render Feature**: Integration with Universal Render Pipeline
- **Global Shader Properties**: Efficient communication without dependencies
- **Two-Pass Rendering**: Optimized shader with separate edge detection and blend passes
- **World-Space Positioning**: Accurate 3D blast sphere effects

## Requirements

- **Unity Version**: Unity 2022.3+ or Unity 6+
- **Render Pipeline**: Universal Render Pipeline (URP) 14+
- **Renderer**: URP Forward Renderer
- **Platform**: All platforms supported by URP

## Installation

### Step 1: Copy Files to Your Project

Copy the entire `ImpactFramesSystem` folder into your Unity project's `Assets` directory:

```
Assets/
└── ImpactFramesSystem/
    ├── Scripts/
    │   └── Effects/
    │       ├── ImpactFrameManager.cs
    │       └── EdgeDetection.cs
    ├── Shaders/
    │   └── ImpactFrameEdgeDetection.shader
    ├── Noise Texture.png
    └── README.md
```

### Step 2: Add Renderer Feature

1. Locate your **URP Renderer** asset (commonly named `PC_Renderer` or similar - typically in `Assets/Settings/`)
2. In the Inspector, click **Add Renderer Feature**
3. Select **Edge Detection** from the dropdown
4. Leave the default settings (or customize as needed)

### Step 3: Create Manager GameObject

1. In your scene Hierarchy, right-click and select **Create Empty**
2. Rename the GameObject to `ImpactFrameManager`
3. Click **Add Component** and search for **Impact Frame Manager**
4. The component will be added and ready to configure

### Step 4: Assign Noise Texture (Recommended)

For the best visual effect:
1. Select the `ImpactFrameManager` GameObject
2. In the Inspector, find the **Noise Settings** section
3. Drag the included `Noise Texture.png` to the **Noise Texture** field
4. Adjust **Noise Intensity** 

## Usage

### Testing in Editor

To test the effect in Play mode:

1. Enter Play mode
2. Select the `ImpactFrameManager` GameObject
3. In the Inspector, find **Test Button (Play Mode Only)**
4. Toggle the `testImpactFrame` checkbox **ON**

### Triggering from Code

Add the namespace at the top of your script:

```csharp
using ImpactFrameSystem;
```

**Basic trigger** (uses default settings):
```csharp
ImpactFrameManager.Instance.TriggerImpactFrame();
```

**Custom duration and intensity**:
```csharp
float duration = 0.5f;    // Effect lasts 0.5 seconds
float intensity = 1.5f;   // 150% intensity
ImpactFrameManager.Instance.TriggerImpactFrame(duration, intensity);
```

**With world position** (for blast sphere effect):
```csharp
Vector3 hitPosition = collision.contacts[0].point;
ImpactFrameManager.Instance.TriggerImpactFrame(0.3f, 1.0f, hitPosition);
```

### Example Integration

```csharp
using UnityEngine;
using ImpactFrameSystem;

public class CombatSystem : MonoBehaviour
{
    private void OnSuccessfulHit(Vector3 hitPosition)
    {
        // Trigger impact frame at hit location
        ImpactFrameManager.Instance.TriggerImpactFrame(0.25f, 1.2f, hitPosition);

        // Continue with your damage logic...
    }

    private void OnCriticalHit(Vector3 hitPosition)
    {
        // Longer, more intense effect for critical hits
        ImpactFrameManager.Instance.TriggerImpactFrame(0.4f, 1.8f, hitPosition);
    }
}
```

## Configuration

### Impact Frame Settings

**Default Duration** (0.2s)
How long the effect lasts in seconds.

**Default Intensity** (1.0)
Overall effect strength. Values above 1.0 create more dramatic effects.

**Intensity Curve**
Animation curve controlling how intensity changes over the duration.
Default: EaseInOut from 1 to 0 (fade out effect)

**Edge Color** (White)
Color of the detected edges. Try bright colors for stylized looks.

**Background Color** (Black)
Background color shown during the effect. Creates contrast with edges.

**Edge Thickness** (3)
Thickness of edge detection lines. Range: 0-15

### Radial Blur Settings

**Use Radial Blur** (true)
Enable/disable radial blur emanating from impact point.

**Blur Strength** (0.5)
Intensity of the radial blur effect.

**Blur Samples** (16)
Quality of blur. Higher = smoother but more expensive.
Range: 8-32

**Blur Radius** (0.5)
Maximum distance of blur from center.

**Additional Blur** (1.0)
Extra blur multiplier for enhanced effect.

### Noise Settings

**Noise Texture**
Grayscale texture for animated line patterns.
Use the included texture or create your own.

**Noise Scale** (1.0)
Scale of the noise pattern. Higher = more frequent lines.

**Noise Intensity** (0.5)
Strength of noise effect. Range: 0-1
0 = no noise, 1 = maximum effect

**Edge Step Threshold** (0.5)
Threshold for noise pattern visibility. Range: 0-1

### Animation Settings

**Animation Speed** (2.0)
Speed of pulsing and line animation. Range: 0-10

### Blast Effect Settings

**Use Blast Effect** (true)
Enable/disable the expanding sphere effect.

**Blast Delay** (0.1s)
Delay before blast sphere starts expanding.

**Blast Radius** (5.0)
Maximum radius of the expanding sphere in world units.

**Blast Duration** (1.0s)
How long the blast takes to fully expand.

**Blast Scale Curve**
Animation curve for sphere expansion.
Default: EaseInOut from 0 to 1

**Blast Intensity** (1.0)
Opacity/strength of blast sphere edges. Range: 0-1

**Blast Edge Thickness** (0.8)
Thickness of the blast sphere edge. Range: 0.1-2.0

## Troubleshooting

### Effect Doesn't Show

**Problem**: Impact frame doesn't appear when triggered.

**Solutions**:
- Verify the **Edge Detection** renderer feature is added to your URP Renderer asset (e.g., `PC_Renderer`, `UniversalRenderer`, etc.)
- Ensure `ImpactFrameManager` exists in the scene
- Check you're using **URP** (not Built-in or HDRP)
- Look for `[ImpactFrame] URP Asset found` in the Console

### Blast Sphere Not Visible

**Problem**: Expanding sphere effect doesn't appear.

**Solutions**:
- Ensure **Use Blast Effect** is enabled
- Verify you're passing a valid `Vector3` world position
- Increase **Blast Radius** (try 10.0 for testing)
- Check **Blast Intensity** is above 0
- Ensure blast duration hasn't already completed

### Black Screen or Weird Colors

**Problem**: Screen goes completely black or shows strange colors.

**Solutions**:
- Check **Default Intensity** isn't set too high (try 1.0)
- Verify **Background Color** alpha is 1.0
- Ensure **Edge Color** is visible against background
- Check Intensity Curve has valid values

### Noise Lines Not Moving

**Problem**: Static edges, no animated lines.

**Solutions**:
- Assign a noise texture to **Noise Texture** field
- Increase **Noise Intensity** above 0
- Verify **Animation Speed** is above 0
- Check noise texture import settings (should be readable)

## API Reference

### ImpactFrameManager

#### Properties

```csharp
public static ImpactFrameManager Instance { get; }
public bool IsActive { get; }
```

#### Methods

```csharp
// Trigger with default settings
public void TriggerImpactFrame()

// Trigger with custom duration and intensity
public void TriggerImpactFrame(float duration, float intensity)

// Trigger with all parameters including world position
public void TriggerImpactFrame(float duration, float intensity, Vector3 worldPosition)

// Stop the current effect immediately
public void StopCurrentEffect()
```

#### Parameters

- `duration`: Effect duration in seconds (-1 uses default)
- `intensity`: Effect strength multiplier (-1 uses default)
- `worldPosition`: World space position for radial blur and blast sphere (Vector3.zero uses screen center)
