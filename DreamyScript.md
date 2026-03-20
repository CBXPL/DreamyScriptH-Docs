# DreamyScript Syntax Documentation

> [!WARNING]
> **This version has major syntax changes. Versions from pre 0.5.0 DSCRH versions wont work on latest syntax unless you update it.**

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
- `Open::Keybind(KEY)` : Sets the toggle key for the script menu.
  - Supported keys: `F1`, `F2`, `F3`, `F4`, `F5`, `HOME`, `INS`.
  - *Quotes are optional* (e.g., `Open::Keybind(F2);` or `Open::Keybind("F2");`).

**Examples:**
```
Open::Keybind(F3);
```
```
Open::Keybind(HOME);
```

----------------------------------------
SECTION: UI CONTROLS
----------------------------------------
Commands used inside `UI::NewWindow` blocks to create interactive menus.
- `UI::NewWindow("Title", width, height) { ... };` : Creates a new ImGui window.
- `UI::Text("Text")` : Displays static text.
- `UI::Value("Label", var)` : Displays the value of an integer variable.
- `UI::InputInt("Label", var)` : Creates an input field for an integer.
- `UI::Checkbox("Label", var)` : Creates a checkbox (1 = checked, 0 = unchecked).
- `UI::SliderInt("Label", var, min, max)` : Creates a slider for integer values.
- `UI::SetWindowPos(x, y)` : Sets the window position. Use `random` for random placement.
- `UI::Button("Label", systemvoid::name)` : Creates a button that executes a defined `systemvoid`.
- `UI::Line` : Draws a horizontal separator line.

**Examples:**
```
** Simple static UI
UI::NewWindow("Status", 200, 50) {
    UI::Text("System Online");
    UI::Line;
};
```
```
** Interactive UI with Logic
UI::Checkbox("Show Stats", showp);
if showp == 1 {
    UI::SliderInt("Scale", uiscale, 1, 100);
};
```

----------------------------------------
SECTION: MEMORY
----------------------------------------
Commands for reading game data.
- `Mem::Read(scope, Field, var)` : Reads game data into a variable.
  - Supported scopes: `local` / `Local`, `Enemy`, `Teammates`.
    - `Enemy`: Reads from the first valid enemy found in the game.
    - `Teammates`: Reads from the first valid teammate found.
  - Supported Fields (Integer/Float):
    - `Velocity` : Current speed of the local player.
    - `ViewAngle_Pitch` : Current vertical aim angle.
    - `ViewAngle_Yaw` : Current horizontal aim angle.
    - `Health` : Current health of the entity (Integer).
    - `Team` : Team ID of the entity (Integer).
    - `Scoped` : Whether the entity is scoped (1 for true, 0 for false) (Integer).
  - Supported Fields (3D Vector):
    - `EntityPosition` : Read 3D coordinates of player's feet.
    - `HeadPos` : Read 3D coordinates of player's head.
    - You can add offsets directly in the field name: `HeadPos(+10.z)` or `EntityPosition(-5.x)`.
    - Supported axes: `.x`, `.y`, `.z`.

**Examples:**
```
** Reading for display
Mem::Read(local, Velocity, speed);
UI::Value("Current Speed", speed);
```
```
** Reading for 3D logic
Mem::Read(local, EntityPosition, my_feet);
Mem::Read(local, HeadPos, my_head);
```

----------------------------------------
SECTION: OVERLAY
----------------------------------------
Commands for drawing 2D text and 3D projected shapes on the screen.

### 2D Screen Text
- `OverlayUI::Text("Text", position)` : Draws text on screen.
  - Positions: `top`, `middle`, `bottom`.
- `OverlayUI::Value("Label", var, position)` : Draws a variable value on screen.

### 3D World Shapes
- `OverlayShape::Square(v_feet, v_head)` : Draws an ESP box between two 3D points.
- `OverlayShape::SquareRounded(v_feet, v_head)` : Draws a rounded corner box.
- `OverlayShape::Circle(v_pos, radius)` : Draws a 3D-positioned circle with screen radius.


**Examples:**
```
** 2D Text
OverlayUI::Text("DMW SCRIPT LOADED", bottom);

** 3D ESP Box
Mem::Read(Enemy, EntityPosition, f);
Mem::Read(Enemy, HeadPos, h);
OverlayShape::Square(f, h);
```

----------------------------------------
SECTION: MATH & LOGIC
----------------------------------------
Arithmetic operations and conditional execution.
- `Math::Op(var1, operator, var2, result_var)` : Performs arithmetic.
  - Operators: `+`, `-`, `*`, `/`.
- `if var operator value { ... };` : Conditional block.
  - Operators: `>`, `<`, `==`, `!=`, `>=`, `<=`.

**Examples:**
```
** Calculation via Button
systemvoid::calc {
    Math::Op(a, +, b, sum);
}
UI::Button("Calculate Summer", systemvoid::calc);
```
```
** Logic check
if v > 300 {
    OverlayUI::Text("SPEED LIMIT EXCEEDED", middle);
};
```

----------------------------------------
SECTION: BLOCKS & STYLING
----------------------------------------
Structure and visual customization.
- `systemvoid::name { ... }` : Defines a reusable block of Math operations.

**Examples:**
```
** Multiple Math ops
systemvoid::complex {
    Math::Op(x, *, 2, x);
    Math::Op(x, +, 10, final_x);
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
    Mem::Read(Enemy, EntityPosition, f);
    Mem::Read(Enemy, HeadPos, h);
    OverlayShape::SquareRounded(f, h);
};
```

----------------------------------------
SECTION: CUSTOMIZATION & THEMES
----------------------------------------
Use `MenuAccent` to customize your script's appearance.

```
MenuAccent {
    menu(30, 30, 30, 255);
    button(180, 0, 255, 255);
}
```
- `menu(r, g, b, a)` : Window background color.
- `button(r, g, b, a)` : Button and interactive elements color.

**Example:**
```
MenuAccent {
    menu(20, 20, 20, 250);
    button(255, 0, 100, 255);
}
```

----------------------------------------
SECTION: KEYBOARD OVERLAY
----------------------------------------
Displays a WASD or WASD+SPACE layout on screen that highlights when keys are pressed.
- `KeyboardAccent { ... }` : Customizes the layout appearance.
    - `scale(value)` : Size multiplier (e.g., 1.2).
    - `pos(x, y)` : Screen coordinates.
    - `outline(r, g, b, a)` : Default color (0-255).
    - `clicked(r, g, b, a)` : Highlight color when key is pressed.
    - `rounding(value)` : Corner rounding.
- `DrawKeyBoardLayout::WSAD` : Draws WASD only.
- `DrawKeyBoardLayout::WSADSPACE` : Draws WASD and SPACE bar.

**Example:**
```
KeyboardAccent {
    scale(1.0);
    pos(100, 100);
    outline(255, 255, 255, 255);
    clicked(255, 0, 0, 255);
    rounding(5.0);
}
DrawKeyBoardLayout::WSADSPACE;
```

----------------------------------------
SECTION: MOUSE OVERLAY
----------------------------------------
Displays a mouse layout on screen that highlights buttons when pressed.
- `MouseAccent { ... }` : Customizes the mouse appearance.
    - `scale(value)` : Size multiplier (e.g., 1.5).
    - `pos(x, y)` : Screen coordinates.
    - `outline(r, g, b, a)` : Default color for the mouse frame.
    - `clicked(r, g, b, a)` : Highlight color when buttons are pressed.
    - `rounding(value)` : Corner rounding for the mouse body.
- `DrawMouseLayout::FULL` : Draws the complete mouse layout (LMB, RMB, MMB/Wheel).

**Example:**
```
MouseAccent {
    scale(1.5);
    pos(500, 1000);
    outline(255, 255, 255, 120);
    clicked(0, 200, 255, 255);
    rounding(50.0);
}
DrawMouseLayout::FULL;
```

----------------------------------------
SECTION: SYNTAX INFO
----------------------------------------
> [!IMPORTANT]
> **New Syntax**: Quotes `""` are now optional for internal variables and commands, but are **REQUIRED for text/titles** for stability.


----------------------------------------
EXAMPLE 1: Calculator (Multiplication)
----------------------------------------
```
** @author Sepeix
** Simple Multiplier
systemvoid::multiply {
    Math::Op(num1, *, num2, result);
}

UI::NewWindow("Calculator", 200, 150) {
    UI::InputInt("Number 1", num1);
    UI::InputInt("Number 2", num2);
    UI::Button("Multiply", systemvoid::multiply);
    UI::Line;
    UI::Value("Result", result);
};
```


----------------------------------------
EXAMPLE 2: FULL HUD (Keyboard & Mouse Viewer)
----------------------------------------
```
** @author Sepeix
Open::Keybind(F2);

MouseAccent {
    scale(1.5);
    pos(500, 1000);
    outline(255, 255, 255, 120);
    clicked(0, 200, 255, 255);
    rounding(50.0);
}
KeyboardAccent {
    scale(1.5);         
    pos(200, 1000);        
    outline(255, 255, 255, 120); 
    clicked(0, 200, 255, 255);  
    rounding(10.0);       
}
DrawMouseLayout::FULL;
DrawKeyBoardLayout::WSADSPACE;
UI::NewWindow("Mouse Viewer Config", 200, 80)
{
};
```
