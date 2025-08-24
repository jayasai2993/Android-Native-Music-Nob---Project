Code Explanation
1. MainActivity → Entry Point
class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            // Jetpack Compose UI goes here
        }
    }
}


ComponentActivity → A base class for activities that use Jetpack Compose.

setContent { ... } → Used to define the UI in Compose DSL (Declarative Syntax) instead of XML.

2. Box and Row → Layout Containers
Box(
    contentAlignment = Alignment.Center,
    modifier = Modifier
        .fillMaxSize()
        .background(Color.Black)
        .padding(20.dp)
) {
    Row(
        horizontalArrangement = Arrangement.Center,
        verticalAlignment = Alignment.CenterVertically,
        modifier = Modifier
            .border(1.dp, Color.White, RoundedCornerShape(10.dp))
            .padding(30.dp)
    ) {


Box → A container that stacks elements on top of each other.

Row → Places items horizontally.

Modifier → Used to modify UI elements (size, padding, rotation, events, etc.).

Alignment.Center → Aligns children to the center.

border(...) → Adds a border.

padding(...) → Adds space inside/outside UI components.

3. State Handling → remember and mutableStateOf
var volume by remember { mutableStateOf(0f) }


remember → Stores a value across recompositions (so UI doesn’t reset when rebuilt).

mutableStateOf → Creates a state variable. When it changes → UI automatically updates.

by and getValue/setValue → Kotlin’s property delegate shorthand.

4. MusicKnob → Custom Rotary Knob
MusicKnob(
    modifier = Modifier.size(100.dp)
) {
    volume = it
}


Displays a knob image (music_knob.png) that rotates when user drags.

Calls onValueChange(percent) whenever the knob rotates, updating the volume.

5. VolumeBar → Visualizer
VolumeBar(
    modifier = Modifier.fillMaxWidth().height(30.dp),
    activeBars = (barCount * volume).roundToInt(),
    barCount = barCount
)


Creates a horizontal bar graph with red (active) and gray (inactive) bars.

activeBars depends on knob’s rotation (volume level).

6. Drawing Bars → Canvas
Canvas(modifier = modifier) {
    for(i in 0 until barCount) {
        drawRoundRect(
            color = if(i in 0..activeBars) Color.Red else Color.DarkGray,
            topLeft = Offset(i * barWidth * 2f + barWidth / 2f, 0f),
            size = Size(barWidth, constraints.maxHeight.toFloat()),
            cornerRadius = CornerRadius(0f)
        )
    }
}


Canvas → Low-level drawing area.

drawRoundRect → Draws each volume bar.

Offset → Represents position (x, y).

Size → Width & height of each rectangle.

7. Knob Rotation Logic
.pointerInteropFilter { event ->
    touchX = event.x
    touchY = event.y
    val angle = -atan2(centerX - touchX, centerY - touchY) * (180f / PI).toFloat()


pointerInteropFilter → Captures touch events like ACTION_DOWN & ACTION_MOVE.

event.x / event.y → Touch coordinates relative to knob.

atan2 → Calculates angle between knob center & touch point.

PI → Mathematical π constant.

Rotation is clamped → between limitingAngle (25°) and 360° - 25°.

rotation = fixedAngle
val percent = (fixedAngle - limitingAngle) / (360f - 2 * limitingAngle)
onValueChange(percent)


rotation → Updates knob image rotation.

percent → Maps angle to a normalized volume (0.0 → 1.0).


👉 In short:

MusicKnob handles touch → calculates angle → rotates knob → sends volume percentage.

VolumeBar receives volume % → lights up red bars accordingly.

Together, they simulate a real-world stereo knob + volume bar.
