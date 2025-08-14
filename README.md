# Ender 3 V2 Pen Plotter - Automated Handwritten Letters

Transform your Ender 3 V2 3D printer into an automated pen plotter for writing personalized handwritten letters using your own handwriting font.

## 📋 Table of Contents
- [Overview](#overview)
- [Features](#features)
- [Requirements](#requirements)
- [Installation](#installation)
- [Complete End-to-End Instructions](#complete-end-to-end-instructions)
- [Configuration](#configuration)
- [Usage](#usage)
- [Calibration Guide](#calibration-guide)
- [Troubleshooting](#troubleshooting)
- [API Reference](#api-reference)
- [Contributing](#contributing)
- [License](#license)

## Overview

This system converts text files into G-code that controls your Ender 3 V2 3D printer with a pen attachment, allowing you to automatically write letters in your own handwriting. Perfect for personalized business correspondence, thank you notes, or any application requiring authentic-looking handwritten documents.

### How It Works
1. Text input → Rendered with your custom TTF font
2. Font outlines → Converted to single-stroke paths
3. Paths → Optimized for minimal travel
4. Optimized paths → G-code with pen up/down commands
5. G-code → SD card → Ender 3 V2 writes your letter

## Features

- ✍️ **Custom Handwriting**: Uses your Caligraphr-generated TTF font
- 📄 **Letter Format**: Automatically formats for standard letter paper (8.5" x 11")
- 🎯 **Smart Text Wrapping**: Automatically wraps long lines
- ⚡ **Path Optimization**: Minimizes pen travel time
- 🖼️ **Preview Generation**: Creates PNG preview before plotting
- 🔧 **Fully Configurable**: Adjustable speeds, heights, and margins
- 📊 **Time Estimation**: Calculates approximate writing time
- 🔄 **Batch Processing**: Support for multiple letters

## Requirements

### Hardware
- Ender 3 V2 3D Printer
- Pen holder attachment for extruder
- Standard letter paper (8.5" x 11")
- Suitable pens (gel or felt-tip recommended)

### Software
- Python 3.7 or higher
- Your custom TTF font file (created with Caligraphr)
- SD card for transferring G-code to printer

### Python Dependencies
```bash
pillow>=9.0.0
numpy>=1.20.0
opencv-python>=4.5.0
opencv-contrib-python>=4.5.0
scipy>=1.7.0
```

## Installation

### Step 1: Clone or Download the Repository
```bash
git clone https://github.com/yourusername/ender3-pen-plotter.git
cd ender3-pen-plotter
```

Or download the files manually:
- `text_to_gcode_plotter.py` (main script)
- `write_letter.py` (simple wrapper)
- `requirements.txt` (dependencies)

### Step 2: Install Python Dependencies
```bash
pip install -r requirements.txt
```

Or install manually:
```bash
pip install pillow numpy opencv-python opencv-contrib-python scipy
```

### Step 3: Configure Your Font Path
Edit `write_letter.py` and set your font path:
```python
FONT_PATH = "path/to/your_handwriting.ttf"
```

## Complete End-to-End Instructions

### 🚀 Full Process Walkthrough

#### Phase 1: Initial Setup (One-time)

1. **Prepare Your Handwriting Font**
   ```bash
   # Ensure your TTF font is accessible
   cp ~/Downloads/MyHandwriting.ttf ./fonts/
   ```

2. **Install the Software**
   ```bash
   # Navigate to your project directory
   mkdir ~/pen-plotter
   cd ~/pen-plotter
   
   # Copy the Python scripts here
   # - text_to_gcode_plotter.py
   # - write_letter.py
   
   # Install dependencies
   pip install pillow numpy opencv-python opencv-contrib-python scipy
   ```

3. **Configure Initial Settings**
   ```bash
   # Edit write_letter.py
   nano write_letter.py
   
   # Update these values:
   FONT_PATH = "./fonts/MyHandwriting.ttf"
   PEN_UP_Z = 5.0      # Start conservative
   PEN_DOWN_Z = 0.5    # Will fine-tune later
   FONT_SIZE = 11      # Standard handwriting size
   WRITE_SPEED = 1200  # Start slow
   ```

#### Phase 2: Hardware Preparation

4. **Mount Pen Holder**
   - Remove filament from extruder
   - Attach pen holder to extruder assembly
   - Insert pen (gel pen recommended)
   - Ensure pen is perpendicular to bed

5. **Prepare Print Bed**
   ```
   # Create paper alignment guides
   - Cut two strips of cardboard
   - Place at X=2.05mm (left edge) and X=217.95mm (right edge)
   - This centers letter paper on 220mm bed
   - Mark top edge at Y=0
   ```

6. **Level the Bed**
   - Use paper leveling method as normal
   - Pay extra attention to center area
   - Ensure consistent height across writing area

#### Phase 3: Calibration Run

7. **Create Calibration Text**
   ```bash
   cat > calibration.txt << EOF
   ABCDEFGHIJKLMNOPQRSTUVWXYZ
   abcdefghijklmnopqrstuvwxyz
   0123456789
   The quick brown fox jumps over the lazy dog.
   This is a test of pen pressure and speed.
   EOF
   ```

8. **Generate First G-code**
   ```bash
   python text_to_gcode_plotter.py calibration.txt \
     -f ./fonts/MyHandwriting.ttf \
     -o calibration.gcode \
     --pen-up 5 \
     --pen-down 0.5 \
     --speed 1200
   ```

9. **Test Print**
   ```bash
   # Copy to SD card (adjust path as needed)
   cp calibration.gcode /media/SD_CARD/
   
   # On printer:
   # 1. Insert SD card
   # 2. Place paper on bed
   # 3. Tape corners with painter's tape
   # 4. Select calibration.gcode
   # 5. Observe carefully
   ```

10. **Fine-tune Z Heights**
    - **If pen doesn't touch**: Decrease PEN_DOWN_Z by 0.2mm
    - **If pen drags hard**: Increase PEN_DOWN_Z by 0.1mm
    - **If pen lifts aren't clean**: Increase PEN_UP_Z
    - Repeat steps 8-9 until perfect

#### Phase 4: Production Letter

11. **Create Your Letter Content**
    ```bash
    cat > letter_to_john.txt << EOF
    Dear Mr. Johnson,
    
    I hope this letter finds you well. I am writing to express my interest 
    in discussing a potential business opportunity with you.
    
    Your company, Johnson's Hardware, has built an impressive reputation 
    in our community over the past 15 years. The dedication to customer 
    service and quality that you've maintained is exactly what I value 
    in a business.
    
    I would greatly appreciate the opportunity to meet with you at your 
    convenience to discuss this further. Please feel free to contact me 
    at your earliest convenience.
    
    Thank you for considering this opportunity.
    
    Sincerely,
    
    
    Robert Smith
    EOF
    ```

12. **Generate Production G-code**
    ```bash
    # Using the wrapper script (easier)
    python write_letter.py letter_to_john.txt JohnsonHardware
    
    # Or using main script directly
    python text_to_gcode_plotter.py letter_to_john.txt \
      -f ./fonts/MyHandwriting.ttf \
      -o letters/johnson_hardware_$(date +%Y%m%d).gcode \
      --pen-up 5 \
      --pen-down 0.3 \
      --speed 1500
    ```

13. **Review Preview**
    ```bash
    # Check the generated preview image
    open letters/johnson_hardware_*_preview.png
    # On Linux: xdg-open
    # On Windows: start
    ```

14. **Print the Letter**
    ```bash
    # Copy to SD card
    cp letters/johnson_hardware_*.gcode /media/SD_CARD/
    
    # On printer:
    # 1. Place fresh letter paper
    # 2. Secure with tape
    # 3. Ensure pen has ink
    # 4. Print the file
    # 5. Monitor first few lines
    ```

15. **Post-Processing**
    - Let ink dry for 30 seconds
    - Carefully remove tape
    - Fold and insert into envelope
    - Add handwritten address (or print envelope too!)

#### Phase 5: Batch Processing (Multiple Letters)

16. **Create Batch Script**
    ```python
    # batch_process.py
    import os
    import time
    from write_letter import write_letter
    
    # List of letters to process
    letters = [
        ("letters/johnson.txt", "Johnson"),
        ("letters/smith.txt", "Smith"),
        ("letters/davis.txt", "Davis"),
    ]
    
    print("Starting batch processing...")
    for filename, recipient in letters:
        print(f"\nProcessing: {recipient}")
        output = write_letter(filename, recipient)
        print(f"Created: {output}")
        time.sleep(1)
    
    print("\n✅ All letters processed!")
    print("Copy .gcode files to SD card")
    ```

17. **Run Batch Process**
    ```bash
    python batch_process.py
    
    # Copy all to SD card
    cp *.gcode /media/SD_CARD/
    ```

#### Phase 6: Integration with LLM Workflow

18. **Automated Pipeline Example**
    ```python
    # llm_to_letter.py
    import openai  # or your LLM of choice
    from write_letter import write_letter
    
    def generate_personalized_letter(business_info):
        # Use LLM to generate letter
        prompt = f"Write a business acquisition letter to {business_info['name']}..."
        letter_text = llm_generate(prompt)
        
        # Save to file
        filename = f"generated_{business_info['id']}.txt"
        with open(filename, 'w') as f:
            f.write(letter_text)
        
        # Convert to G-code
        gcode_file = write_letter(filename, business_info['name'])
        
        return gcode_file
    ```

### ✅ Success Checklist
- [ ] Font file path configured correctly
- [ ] Dependencies installed successfully
- [ ] Pen holder mounted securely
- [ ] Bed leveled properly
- [ ] Paper alignment guides in place
- [ ] Calibration print successful
- [ ] Z-heights optimized
- [ ] Writing speed optimized
- [ ] First letter printed successfully
- [ ] Batch processing tested

## Configuration

### Key Parameters

| Parameter | Default | Description | Adjustment Guide |
|-----------|---------|-------------|------------------|
| `FONT_SIZE` | 11mm | Height of capital letters | 8-15mm typical range |
| `PEN_UP_Z` | 5mm | Height when traveling | 3-10mm, minimize for speed |
| `PEN_DOWN_Z` | 0.2mm | Height when writing | 0-1mm, adjust for pen type |
| `WRITE_SPEED` | 1500 mm/min | Drawing speed | 800-3000, start low |
| `TRAVEL_SPEED` | 3000 mm/min | Travel speed | 2000-5000 typical |

### Paper Settings

```python
# In text_to_gcode_plotter.py
self.paper_width = 215.9    # 8.5 inches
self.paper_height = 279.4   # 11 inches
self.margin_x = 25.4        # 1 inch margins
self.margin_top = 25.4      # 1 inch top margin
```

## Usage

### Basic Usage
```bash
python text_to_gcode_plotter.py input.txt -f font.ttf -o output.gcode
```

### With All Options
```bash
python text_to_gcode_plotter.py input.txt \
  --font ./fonts/handwriting.ttf \
  --output letter.gcode \
  --font-size 12 \
  --pen-up 5 \
  --pen-down 0.2 \
  --speed 1500 \
  --travel-speed 3000
```

### Using Wrapper Script
```bash
python write_letter.py letter.txt "John Doe"
```

## Calibration Guide

### Step-by-Step Calibration

1. **Start with test pattern**
2. **Adjust PEN_DOWN_Z** until pen barely touches paper
3. **Increase WRITE_SPEED** gradually until quality degrades
4. **Back off 20% from maximum speed**
5. **Fine-tune FONT_SIZE** for readability

### Pen Pressure Guide

| Pen Type | Recommended PEN_DOWN_Z | Notes |
|----------|------------------------|-------|
| Gel | 0.1 - 0.3mm | Light pressure |
| Ballpoint | 0.3 - 0.5mm | Needs more pressure |
| Felt-tip | 0.0 - 0.2mm | Very light touch |
| Fountain | 0.0 - 0.1mm | Minimal pressure |

## Troubleshooting

### Common Issues and Solutions

| Problem | Possible Cause | Solution |
|---------|---------------|----------|
| **Incomplete letters** | Pen lifting mid-stroke | Reduce speed, check pen flow |
| **Double lines** | Font rendering issue | Check font is single-stroke compatible |
| **Jagged lines** | Low resolution | Increase DPI in script (default 300) |
| **Pen skipping** | Too fast or wrong pressure | Reduce speed, adjust Z height |
| **Text too small** | Font size setting | Increase FONT_SIZE parameter |
| **Crashes into bed** | Wrong Z calibration | Increase PEN_DOWN_Z immediately |
| **Takes forever** | Speeds too conservative | Increase both speed parameters |
| **Uneven writing** | Bed not level | Re-level bed, check paper placement |

### Debug Mode
```python
# Add to script for verbose output
converter.debug = True  # Shows path optimization details
```

## API Reference

### Main Class: `TextToGcodePlotter`

```python
TextToGcodePlotter(
    font_path: str,           # Path to TTF font
    font_size: float = 12,    # Size in mm
    pen_up_z: float = 5,      # Lift height
    pen_down_z: float = 0,    # Writing height
    feed_rate: int = 1500,    # Write speed mm/min
    travel_rate: int = 3000   # Travel speed mm/min
)
```

### Methods

#### `convert_text_to_gcode(text: str, output_file: str)`
Main conversion method. Generates G-code and preview image.

#### `text_to_image(text: str, font_size_pt: int) -> np.array`
Renders text to grayscale image.

#### `image_to_paths(img: np.array) -> List[List[Tuple]]`
Converts image to vector paths.

#### `optimize_paths(paths: List) -> List`
Optimizes path order for minimal travel.

#### `paths_to_gcode(paths: List) -> str`
Generates G-code from paths.

## Contributing

Contributions are welcome! Areas for improvement:
- SVG font support
- Cursive connection optimization
- Pressure variation for realistic writing
- Web interface for easier use
- Direct SD card writing

## License

MIT License - feel free to use and modify for your needs.

## Credits

Created for automating handwritten business correspondence using the Ender 3 V2 3D printer.

---

**Happy Plotting!** 🖊️🤖
