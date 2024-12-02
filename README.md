# Ejercicio_clase_8

Este código define clases para representar puntos, líneas y rectángulos. La clase `Point` almacena las coordenadas 𝑥 y y, mientras que la clase `Line` calcula algunas propiedades como su longitud, pendiente y si cruza los ejes horizontales y verticales. La clase `Rectangle` permite crear un rectángulo de diferentes maneras (por ejemplo, con coordenadas de esquinas o líneas). En el caso de que el rectángulo se construya utilizando líneas, estas deben ser paralelas a los ejes coordenados. Adicionalmente, el código incluye una función para discretizar una línea en puntos. 

``` python
import math

class Point:
    def __init__(self, x: float, y: float):
        self.x = x
        self.y = y

class Line:
    def __init__(self, start: Point, end: Point):
        self.length = 0
        self.slope = 0
        self.start = start
        self.end = end
        self.points = []

    def compute_length(self) -> float:
        self.length = math.sqrt((self.end.x - self.start.x) ** 2 + (self.end.y - self.start.y) ** 2)
        return self.length

    def compute_slope(self) -> float:
        slope_in_radians = math.atan2(abs(self.end.y - self.start.y), abs(self.end.x - self.start.x))
        self.slope = (slope_in_radians * 180) / math.pi # Convert to degrees
        return self.slope

    def compute_horizontal_cross(self) -> bool:
        # Returns True if the points are on opposite sides or are on the horizontal axis
        return self.start.y * self.end.y <= 0

    def compute_vertical_cross(self) -> bool:
        # Returns True if the points are on opposite sides or are on the vertical axis
        return self.start.x * self.end.x <= 0

    def discretize_line(self, n: int):
        self.points.clear()

        # Calculate the distance between points
        dx = (self.end.x - self.start.x) / (n - 1)
        dy = (self.end.y - self.start.y) / (n - 1)

        # Append the points to the list
        for i in range(n):
            new_x = self.start.x + i * dx
            new_y = self.start.y + i * dy
            self.points.append(Point(new_x, new_y))
            print(f"Points: ({new_x}, {new_y})")

class Rectangle:
    def __init__(self, method: int, *args):
        if method == 1:  # Bottom-left corner + width and height
            self.bottom_left, self.width, self.height = args
            self.center = Point(self.bottom_left.x + self.width / 2, self.bottom_left.y + self.height / 2)
            self.upper_right = Point(self.bottom_left.x + self.width, self.bottom_left.y + self.height)
        elif method == 2:  # Center + width and height
            self.center, self.width, self.height = args
            self.bottom_left = Point(self.center.x - self.width / 2, self.center.y - self.height / 2)
            self.upper_right = Point(self.center.x + self.width / 2, self.center.y + self.height / 2)
        elif method == 3:  # Two opposite corners (bottom-left and upper-right)
            self.bottom_left, self.upper_right = args
            self.width, self.height = self.upper_right.x - self.bottom_left.x, self.upper_right.y - self.bottom_left.y
            self.center = Point(self.bottom_left.x + self.width / 2, self.bottom_left.y + self.height / 2)
        elif method == 4:  # Four lines
            # Determine the vertical and horizontal lines
            vertical_lines = []
            horizontal_lines = []
            for line in args:
                slope = line.compute_slope()
                if slope == 90:
                    vertical_lines.append(line)
                elif slope == 0:
                    horizontal_lines.append(line)

            # Validate that there are 2 vertical lines and 2 horizontal lines
            if len(vertical_lines) != 2 or len(horizontal_lines) != 2:
                raise ValueError("There must be 4 lines")

            # Sort vertical and horizontal lines by their coordinates
            vertical_lines.sort(key=lambda l: min(l.start.x, l.end.x))
            horizontal_lines.sort(key=lambda l: min(l.start.y, l.end.y))

            # Ensure the lines intersect correctly
            x_condition = vertical_lines[0].start.x == horizontal_lines[0].start.x or vertical_lines[0].start.x == horizontal_lines[0].end.x
            y_condition = vertical_lines[1].end.y == horizontal_lines[1].start.y or vertical_lines[1].end.y == horizontal_lines[1].end.y

            if not x_condition or not y_condition:
                raise ValueError("The coordinates of the intersection points do not match")
```

### Ejemplo de uso 1 (rectángulo construido correctamente)
``` python
# Define the corners of the rectangle
point1 = Point(0, 0)
point2 = Point(4, 0)
point3 = Point(4, 2)
point4 = Point(0, 2)

# Define the sides of the rectangle
line1 = Line(point4, point3)
line2 = Line(point2, point3)
line3 = Line(point1, point4)
line4 = Line(point1, point2)

# Discretize line and display points
print("Points on the line:")
line1.discretize_line(3)

# Display line properties
print("Line length:", line1.compute_length())
print("Line slope:", line1.compute_slope())
print("Crosses the horizontal axis?:", line1.compute_horizontal_cross())
print("Crosses the vertical axis?:", line1.compute_vertical_cross())

# Create a rectangle using the lines 
rectangle = Rectangle(4, line1, line4, line2, line3)
```

### Output
Points on the line:

Points: (0.0, 2.0)

Points: (2.0, 2.0)

Points: (4.0, 2.0)

Line length: 4.0

Line slope: 0.0

Crosses the horizontal axis?: False

Crosses the vertical axis?: True


### Ejemplo de uso 2 (Error al construir el rectángulo)
``` python
point1 = Point(0, 0)
point2 = Point(2, 0)
point3 = Point(5, 0)
point4 = Point(5, 2)
point5 = Point(3, 2)
point6 = Point(0, 2)

line1 = Line(point1, point6)
line2 = Line(point2, point3)
line3 = Line(point3, point4)
line4 = Line(point5, point6)

# Create a rectangle using the lines 
rectangle = Rectangle(4, line1, line4, line2, line3)
```

### Output
ValueError: The coordinates of the intersection points do not match"



