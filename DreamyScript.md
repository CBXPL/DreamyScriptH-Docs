# DreamyScript Syntax Documentation

DreamyScript is a custom, high-performance scripting language for DreamyWare External, designed for easy UI creation, memory reading, and 3D overlays.

----------------------------------------
SECTION: METADATA
----------------------------------------
Scripts should start with metadata for identification.
- `** @author Name` : Sets the author of the script.

**Examples:**
```
** @author Sepeix
```
```
** @author Pabello
```

----------------------------------------
SECTION: CORE
----------------------------------------
Basic configuration for the script environment.
- `Open::Keybind("KEY")` : Sets the toggle key for the script menu.
  - Supported keys: `"F1"`, `"F2"`, `"F3"`, `"F4"`, `"F5"`, `"HOME"`, `"INS"`.

**Examples:**
```
Open::Keybind("F3");
```
```
Open::Keybind("HOME");
```

----------------------------------------
SECTION: UI
----------------------------------------
Commands used inside `UI::NewWindow` blocks to create interactive menus.
- `UI::NewWindow("Title", width, height) { ... };` : Creates a new ImGui window.
- `UI::Text("Text")` : Displays static text.
- `UI::Value("Label", "var")` : Displays the value of an integer variable.
- `UI::InputInt("Label", "var")` : Creates an input field for an integer.
- `UI::Checkbox("Label", "var")` : Creates a checkbox (1 = checked, 0 = unchecked).
- `UI::SliderInt("Label", "var", min, max)` : Creates a slider for integer values.
- `UI::SetWindowPos("x", "y")` : Sets the window position. Use `"random"` for random placement.
- `UI::Button("Label", systemvoid::name)` : Creates a button that executes a defined `systemvoid`.
- `UI::Line` : Draws a horizontal separator line.

**Examples:**
```
// Simple static UI
UI::NewWindow("Status", 200, 50) {
    UI::Text("System Online");
    UI::Line;
};
```
```
// Interactive UI with Logic
UI::Checkbox("Show Stats", "showp");
if "showp" == 1 {
    UI::SliderInt("Scale", "uiscale", 1, 100);
};
```

----------------------------------------
SECTION: MEMORY
----------------------------------------
Commands for reading game data using protected direct syscalls.
- `Mem::Read("scope", "Field", "var")` : Reads game data into a variable.
  - Supported scopes: `"local"` / `"Local"`, `"Enemy"`, `"Teammates"`.
    - `"Enemy"`: Reads from the first valid enemy found in the game.
    - `"Teammates"`: Reads from the first valid teammate found.
  - Supported Fields (Integer/Float):
    - `"Velocity"` : Current speed of the local player.
    - `"ViewAngle_Pitch"` : Current vertical aim angle.
    - `"ViewAngle_Yaw"` : Current horizontal aim angle.
    - `"Health"` : Current health of the entity (Integer).
    - `"Team"` : Team ID of the entity (Integer).
    - `"Scoped"` : Whether the entity is scoped (1 for true, 0 for false) (Integer).
  - Supported Fields (3D Vector):
    - `"EntityPosition"` : Read 3D coordinates of player's feet.
    - `"HeadPos"` : Read 3D coordinates of player's head.
    - You can add offsets directly in the field name: `HeadPos(+10.z)` or `EntityPosition(-5.x)`.
    - Supported axes: `.x`, `.y`, `.z`.

**Examples:**
```
// Reading for display
Mem::Read("local", "Velocity", "speed");
UI::Value("Current Speed", "speed");
```
```
// Reading for 3D logic
Mem::Read("local", "EntityPosition", "my_feet");
Mem::Read("local", "HeadPos", "my_head");
```

----------------------------------------
SECTION: OVERLAY
----------------------------------------
Commands for drawing 2D text and 3D projected shapes on the screen.

### 2D Screen Text
- `OverlayUI::Text("Text", "position")` : Draws text on screen.
  - Positions: `"top"`, `"middle"`, `"bottom"`.
- `OverlayUI::Value("Label", "var", "position")` : Draws a variable value on screen.

### 3D World Shapes
- `OverlayShape::Square("v_feet", "v_head")` : Draws an ESP box between two 3D points.
- `OverlayShape::SquareRounded("v_feet", "v_head")` : Draws a rounded corner box.
- `OverlayShape::Circle("v_pos", radius)` : Draws a 3D-positioned circle with screen radius.


**Examples:**
```
// 2D Text
OverlayUI::Text("DMW SCRIPT LOADED", "bottom");

// 3D ESP Box
Mem::Read("Enemy", "EntityPosition", "f");
Mem::Read("Enemy", "HeadPos", "h");
OverlayShape::Square("f", "h");
```

----------------------------------------
SECTION: MATH & LOGIC
----------------------------------------
Arithmetic operations and conditional execution.
- `Math::Op("var1", "operator", "var2", "result_var")` : Performs arithmetic.
  - Operators: `"+"`, `"-"`, `*`, `"/"`.
- `if "var" operator value { ... };` : Conditional block.
  - Operators: `>`, `<`, `==`, `!=`, `>=`, `<=`.

**Examples:**
```
// Calculation via Button
systemvoid::calc {
    Math::Op("a", "+", "b", "sum");
}
UI::Button("Calculate Summer", systemvoid::calc);
```
```
// Logic check
if "v" > 300 {
    OverlayUI::Text("SPEED LIMIT EXCEEDED", "middle");
};
```

----------------------------------------
SECTION: BLOCKS & STYLING
----------------------------------------
Structure and visual customization.
- `systemvoid::name { ... }` : Defines a reusable block of Math operations.
- `void::accent { menu::accent(r, g, b, a); button::accent(r, g, b, a); }` : Sets custom UI colors (0-255).

**Examples:**
```
// Custom Theme
void::accent {
    menu::accent(10, 10, 10, 200);
    button::accent(255, 0, 0, 255);
}
```
```
// Multiple Math ops
systemvoid::complex {
    Math::Op("x", "*", "2", "x");
    Math::Op("x", "+", "10", "final_x");
}
```


SECTION: LOOPS (FOR EACH)
----------------------------------------
Used to run a block of code for multiple entities (e.g., all enemies).
- `ForEach::Enemy { ... };` : Executes for every valid enemy.
- `ForEach::Teammates { ... };` : Executes for every valid teammate.

**Example: Full ESP for all enemies**
```
ForEach::Enemy
{
    Mem::Read("Enemy", "EntityPosition", "f");
    Mem::Read("Enemy", "HeadPos", "h");
    OverlayShape::SquareRounded("f", "h");
};
```

----------------------------------------
EXAMPLE SCRIPT
----------------------------------------
```
** @author DreamyUser
Open::Keybind("F1");

void::accent {
    menu::accent(30, 30, 30, 255);
    button::accent(180, 0, 255, 255);
}

UI::NewWindow("Speed Meter", 200, 100)
{
    Mem::Read("local", "Velocity", "speed");
    UI::Value("Current Speed", "speed");
    
    if "speed" > 250
    {
        UI::Text("Bhop Master!");
        OverlayUI::Text("GOD SPEED", "top");
    };
};
```
