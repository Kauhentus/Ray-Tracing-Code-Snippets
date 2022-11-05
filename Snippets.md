# Ray-Tracing-Code-Snippets

# Snippet 1
```html
<!DOCTYPE html>
<html lang="en">
<head><title>Ray Tracing Demo</title></head>
<body class="flex w-screen h-screen">
<canvas id="canvas" style="background-color: black;"></canvas>
<script>

/* Your code goes here!*/


</script>
</body>
</html>
```

# Snippet 2
```js
const W = 300 * 4;
const H = 225 * 4;
const EPS = 1e-8;

/** @type {HTMLCanvasElement} **/
const canvas = document.getElementById('canvas');
const ctx = canvas.getContext('2d')
canvas.width = W;
canvas.height = H;

ctx.fillStyle = 'black'
ctx.fillRect(0, 0, W, H);
```

# Snippet 3
```js
const imageData = ctx.createImageData(W, H);
const pixels = imageData.data; // [# pixels * 4 channels]

for(let y = 0; y < H; y++){
	for(let x = 0; x < W; x++){
		const i = (y * W + x) * 4;
    
		pixels[i] = 0;		      // R
		pixels[i + 1] = 125;	  // G
		pixels[i + 2] = 255;	  // B
		pixels[i + 3] = 255;		// A
	}
}
console.log("DONE")
ctx.putImageData(imageData, 0, 0);
```

# Snippet 4
```js
class Ray {
    constructor(origin, direction){
        this.origin = origin;
        this.direction = direction;
    }
}

const focalLength = -1;
const vertical_field_of_view = Math.PI / 4;
const rayFromCameraPixel = (x, y) => {
    const Hp = -2 * Math.tan(vertical_field_of_view / 2);
	const Wp = Hp * W / H;
    const ray_origin = [
		focalLength * (x / W - 0.5) * Wp,
		focalLength * -(y / H - 0.5) * Hp,
        focalLength
    ];
    const ray_direction = vec3_normalize(ray_origin);
    return new Ray(ray_origin, ray_direction);
}
```

# Snippet 5
```js
class Sphere {
	constructor(position, radius, color, reflective, specular = 1){
		this.position = position;
		this.radius = radius;
		this.color = color;
		this.reflective = reflective;
		this.specular = specular;
	}
}
const sphere_array = [
	new Sphere([0, 0, -13], 3, [255, 255, 255], 0.5, 10)
];
```

# Snippet 6
```js
const ray_sphere_intersection = (point, direction, sphere) => {
    const vec_between = vec3_sub(point, sphere.position);
    const a = 1;
    const b = 2 * vec3_dot(direction, vec_between);
    const c = vec3_mag_squared(vec_between) - sphere.radius ** 2;
    if(b**2 - 4*a*c > 0) {
		return (-b - Math.sqrt(b**2 - 4*a*c)) / (2*a);
	}
    else {
		return null;
	}
}
```

# Snippet 7
```js
const closest_intersection = (ray) => {
	let closest_t = Infinity;
	let closestSphere = null;

	for(let sphere of sphere_array){
		const new_t = ray_sphere_intersection(ray.origin, ray.direction, sphere);
		if(new_t != null && new_t < closest_t && EPS < new_t){
			closest_t = new_t;
			closestSphere = sphere;
		}
	}

	return [closestSphere, closest_t];
}
```

# Snippet 8
```js
const trace_ray = (ray) => {
	let closest_intersection_output = closest_intersection(ray);
	let [closestSphere, closest_t] = closest_intersection_output;

	if(closestSphere == null) return [0, 0, 0];
  else return [255, 255, 255];
}

/* ... */

for(let y = 0; y < H; y++){
	for(let x = 0; x < W; x++){
		const i = (y * W + x) * 4;

		const ray = rayFromCameraPixel(x, y);
		const color = trace_ray(ray);
		
		pixels[i] = color[0];		  // R
		pixels[i + 1] = color[1];	// G
		pixels[i + 2] = color[2];	// B
		pixels[i + 3] = 255;		  // A
	}
}
```
