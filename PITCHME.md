15/28

program.apply

buffer.VERTEX

@snap[north span-40]
![OPENGL](assets/img/opengl_logo.png)
@snapend

@snap[south]
@ul[list-spaced-bullets text-07]
- developed by SGI in 1991
- last release by SGI in July 2006 (2.1)
- first big release by Khronos in August 2008 (3.0)
- July 2010, 4.1 (last available on MacOS)
- last release, 4.6, on July 2017
@ulend
@snapend

---

### Welcome *GLN*

- inline classes (and "enums")
- inline functions
- object oriented
- DSL
- functional programming
- convenience overloading functions
- glm interoperability

---

```kotlin
fun main() {
	var program = 0
	enum class Buffer { VERTEX, ELEMENT }
	var buffers = IntBuffer(2)
	var vertexArray = 0
	var uniformMVP = 0
	var uniformDiffuse = 0
	initProgram()
	initBuffers()
	initVertexArray()
	render()
	end()
}
```

@[8, zoom-13]

---

```kotlin zoom-07
fun initProgram() {
    program = GL20C.glCreateProgram() // Int
	val vertex = initVertexShader() // Int
	val fragment = initFragmentShader() // Int
	GL20C.glAttachShader(program, vertex)
	GL20C.glAttachShader(program, fragment)
	GL20C.glBindAttribLocation(program, semantic.attr.POSITION, "Position")
	GL20C.glBindAttribLocation(program, semantic.attr.COLOR, "Color")
	GL20C.glLinkProgram(program)
	val linkStatus = GL20C.glGetProgrami(program, GL20C.GL_LINK_STATUS) // Int
	if(linkStatus ++ GL20C.GL_FALSE) {
		val infoLogLenght = GL20C.glGetProgrami(shader, GL20C.GL_INFO_LOG_LENGTH) // Int
		val infoLog = GL20C.glGetProgramInfoLog(shader, infoLogLenght) // String
		throw Exception("program link status false: $infoLog")
	}
	GL20C.glDetachShader(program, vertex)
	GL20C.glDetachShader(program, fragment)
	GL20C.glDeleteShader(vertex)
	GL20C.glDeleteShader(fragment)
}
```

@[2, zoom-13]
@[3, zoom-13]

---

```kotlin code-reveal-fast zoom-07
fun initVertexShader(): Int {
	val source = """
		#version 300 es
		uniform mat4 MVP;
		in vec2 Position; // layout (location = ${semantic.attr.POSITION}
		in vec3 Color;
		out vec3 c;
		void main()
		{	
			gl_Position = MVP * Position;
			c = Color;
		}
	"""
	val vertex = GL20C.glCreateShader(GL20C.GL_VERTEX_SHADER)
	initShader(vertex, source)
	return vertex
}

fun initShader(shader: Int, source: String) {
  GL20C.glShaderSource(shader, source)
  GL20C.glCompileShader(shader)
  val compileStatus = GL20C.glGetShaderi(name, GL20C.GL_COMPILE_STATUS) // Int
  if(compileStatus == GL20C.GL_FALSE) {
	val infoLogLenght = GL20C.glGetShaderi(shader, GL20C.GL_INFO_LOG_LENGTH) // Int
	val infoLog = GL20C.glGetShaderInfoLog(shader, infoLogLenght) // String
	throw Exception("shader compile status false: $infoLog")
  }
}
```

@[1-17]
@[2-13, zoom-13]
@[14]
@[15, zoom-13]
@[19-27]
@[20, zoom-12]
@[21, zoom-12]
@[22, zoom-12]
@[23,27, zoom-12]
@[24, zoom-12]
@[25, zoom-12]
@[26, zoom-12]
@[15, zoom-12]
@[16, zoom-12]

---

```kotlin code-reveal-fast zoom-06
fun initVertexShader(): Int {
	val source = ...
	val vertex = GL20C.glCreateShader(GL20C.GL_VERTEX_SHADER)
	initShader(vertex, source)
	return vertex
}

fun initShader(shader: Int, source: String) {
  GL20C.glShaderSource(shader, source)
  GL20C.glCompileShader(shader)
  val compileStatus = GL20C.glGetShaderi(name, GL20C.GL_COMPILE_STATUS) // Int
  if(compileStatus == GL20C.GL_FALSE) {
	val infoLogLenght = GL20C.glGetShaderi(shader, GL20C.GL_INFO_LOG_LENGTH) // Int
	val infoLog = GL20C.glGetShaderInfoLog(shader, infoLogLenght) // String
	throw Exception("shader compile status false: $infoLog")
  }
}

fun initVertexShader(): GlShader {
	val source = ...
	return GlShader.create(VERTEX_SHADER).with { // this: GlShader
		source(source)
		compile()
		if(!compileStatus) 
			throw Exception("shader compile status false: $infoLog")
	}
```

@[1,19, zoom-12]
@[2,20, zoom-12]
@[3,21, zoom-12]
@[4,8-9,21-22,26, zoom-12]
@[10,21,23,26, zoom-12]
@[11-12,16,21,24,26, zoom-12]
@[13-15,21,25,26, zoom-12]

---

@snap[north]
```kotlin





	.. GlShader.create(VERTEX_SHADER) ..
```
@snapend

---

@snap[north]
```kotlin





	.. GlShader.create(VERTEX_SHADER) ..
```
@snapend
inline class or enum?

---
@snap[north]
```kotlin





	.. GlShader.create(VERTEX_SHADER) ..
```
@snapend
inline class or enum?

Both!

---

```kotlin 
inline class ShaderType(val i: Int) {
    companion object {
        val COMPUTE_SHADER = ShaderType(GL43.GL_COMPUTE_SHADER)
        val VERTEX_SHADER = ShaderType(GL20.GL_VERTEX_SHADER)
        val TESS_CONTROL_SHADER = ShaderType(GL40.GL_TESS_CONTROL_SHADER)
        val TESS_EVALUATION_SHADER = ShaderType(GL40.GL_TESS_EVALUATION_SHADER)
        val GEOMETRY_SHADER = ShaderType(GL32.GL_GEOMETRY_SHADER)
        val FRAGMENT_SHADER = ShaderType(GL20.GL_FRAGMENT_SHADER)
    }
}
```

---

```kotlin code-reveal-fast zoom-07
fun initProgram() {
    program = GL20C.glCreateProgram() // Int
	val vertex = createVertexShader() // Int
	val fragment = createFragmentShader() // Int
	GL20C.glAttachShader(program, vertex)
	GL20C.glAttachShader(program, fragment)
	GL20C.glBindAttribLocation(program, semantic.attr.POSITION, "Position")
	GL20C.glBindAttribLocation(program, semantic.attr.COLOR, "COLOR")
	GL20C.glLinkProgram(program)
	...
}

fun initProgram() {
    program = GlProgram.create().apply { // this: GlProgram
		val vertex = createVertexShader() // GlShader
		val fragment = createFragmentShader() // GlShader
		this += vertex
		this += fragment
		"Position".attrib = semantic.attr.POSITION
		"Color".attrib = semantic.attr.COLOR
		link()
		if(!linkStatus) 
			throw Exception("program link status false: $infoLog")
		this -= vertex
		this -= fragment
		vertex.delete()
		fragment.delete()
	}
}
```

@[1-10]
@[4, zoom-13]
@[5-6, zoom-13]
@[7-8]
@[9, zoom-13]
@[10]
@[1,13]
@[2,14,28]
@[3-4,14-16,28]
@[5-6,14,17-18,28]
@[7,8,14,19-20,28]
@[9,14,21,28]
@[10]

---

```kotlin code-reveal-fast zoom-07
fun initProgram() {
	...
    val linkStatus = GL20C.glGetProgrami(program, GL20C.GL_LINK_STATUS) // Int
	if(linkStatus == GL20C.GL_FALSE) {
		val infoLogLenght = GL20C.glGetProgrami(shader, GL20C.GL_INFO_LOG_LENGTH) // Int
		val infoLog = GL20C.glGetProgramInfoLog(shader, infoLogLenght) // String
		throw Exception("program link status false: $infoLog")
	}
	GL20C.glDetachShader(program, vertex)
	GL20C.glDetachShader(program, fragment)
	GL20C.glDeleteShader(vertex)
	GL20C.glDeleteShader(fragment)
}

fun initProgram() {
    program = GlProgram.create().apply { // this: GlProgram
		val vertex = createVertexShader() // GlShader
		val fragment = createFragmentShader() // GlShader
		this += vertex
		this += fragment
		"Position".attrib = semantic.attr.POSITION
		"Color".attrib = semantic.attr.COLOR
		link()
		if(!linkStatus) 
			throw Exception("program link status false: $infoLog")
		this -= vertex
		this -= fragment
		vertex.delete()
		fragment.delete()
	}
}
```

@[2]
@[3-4, 24]
@[5-7, 25]
@[9-10, 26-27]
@[11-12, 28-29]

---

```kotlin 
fun initProgram() {
	val program = GlProgram.initFromPath(SHADER_SOURCE)
}
```

---

```kotlin zoom-08
fun initProgram() {
	val program = GlProgram.initFromPath(SHADER_SOURCE) { // this: ProgramBase
		"Position".attrib = semantic.attr.POSITION
	}
}
```

---

```kotlin 
fun main() {
	var program = 0
	var buffers = IntBuffer(2)
	var vertexArray = 0
	var uniformMVP = 0
	var uniformDiffuse = 0
	initProgram()
	initBuffers()
	initVertexArray()
	render()
	end()
}
```

@[8]

---

```kotlin code-reveal-fast zoom-07
fun initBuffer(): Boolean {

	GL15C.glGenBuffers(buffers)
	
	GL15C.glBindBuffer(GL15C.GL_ARRAY_BUFFER, buffers[Buffer.VERTEX])
	GL15C.glBufferData(GL15C.GL_ARRAY_BUFFER, positions.data, GL15C.GL_STATIC_DRAW)
	GL15C.glBindBuffer(GL15C.GL_ARRAY_BUFFER, 0)
		
	GL15C.glBindBuffer(GL15C.GL_ELEMENT_ARRAY_BUFFER, buffers[Buffer.ELEMENT])
	GL15C.glBufferData(GL15C.GL_ELEMENT_ARRAY_BUFFER, elements, GL15C.GL_STATIC_DRAW)
	GL15C.glBindBuffer(GL15C.GL_ELEMENT_ARRAY_BUFFER, 0)
	
	return checkError("initBuffer") // glGetError() == GL_NONE
}

fun initBuffer(): Boolean {

	buffers.gen { // this: GlBuffersDsl
		Buffer.VERTEX.bound(ARRAY) { 
			data(positions.data) 
		}
		Buffer.ELEMENT.bound(ELEMENT_ARRAY) { 
			data(elements) 
		}
	}

	return checkError("initBuffer")
}
```

@[1-14]
@[3, zoom-13]
@[5]
@[6]
@[7]
@[9]
@[10]
@[11]
@[13]
@[1, 16]
@[3, 18,25]
@[5,7, 19,21]
@[6, 20]
@[9,11, 22,24]
@[10, 23]
@[13, 27]

---

```kotlin zoom-07
fun initVertexArray(): Boolean {
	vertexArray = GL30C.glGenVertexArrays()	
	GL30C.glBindVertexArray(vertexArray)	
	GL15C.glBindBuffer(GL15C.GL_ARRAY_BUFFER, buffers[Buffer.VERTEX])
	GL20C.glVertexAttribPointer(semantic.attr.POSITION, Vec2.length, GL20C.GL_FLOAT, 
								false, Vec2.size + Vec3.size, 0L)
	GL20C.glVertexAttribPointer(semantic.attr.COLOR, Vec3.length, GL20C.GL_FLOAT, 
								false, Vec2.size + Vec3.size, Vec2.size)
	GL15C.glBindBuffer(GL15C.GL_ARRAY_BUFFER, 0)	
	GL15C.glBindBuffer(GL15C.GL_ELEMENT_ARRAY_BUFFER, buffers[Buffer.ELEMENT])
	GL20C.glEnableVertexAttribArray(semantic.attr.POSITION)	
	GL20C.glEnableVertexAttribArray(semantic.attr.COLOR)	
	GL30C.glBindVertexArray(0)
	return checkError("initVertexArray")
}

fun initVertexArray(): Boolean {
	gl.genVertexArrays(::vertexArray)
			.bound {
				buffers {
					Buffer.VERTEX.bound(ARRAY) {
						glf.pos2col3.set()
					}
					Buffer.ELEMENT bind ELEMENT_ARRAY
				}
				glf.pos2col3.enable()
			}
	return checkError("initVertexArray")
}

```

@[2]
@[3]
@[4]
@[5-8]
@[9]
@[10]
@[11-12]
@[13]
@[14]
@[1,17]
@[2,18]
@[3,13, 19,27]
@[4,20,25]
@[4,9, 20-21,23,25]
@[5-8, 20,22,25]
@[10, 20,24,25]
@[11-12, 26]
@[14, 28]

---

```kotlin zoom-08
val windowSize: Vec2i ..

fun render(): Boolean {

	val projection = glm.perspective(glm.πf * 0.25f, windowSize.aspect, 0.1f, 100f)
	val model = Mat4(1f)
	val view = Mat4(1f).translate(0f, 0f, -4f)
	val mvp = projection * view * model

	glViewport(0, 0, 800, 600) // glViewport(windowSize)

	glClearColor(0f, 0f, 0f, 1f)
	glClearDepthf(1f)
	glClear(GL_COLOR_BUFFER_BIT or GL_DEPTH_BUFFER_BIT)

	...

	return true
}
```

@[1]
@[3,19]
@[5]
@[6]
@[7]
@[8]
@[10]
@[12]
@[13]
@[14]
@[16]

---


```kotlin zoom-08 code-reveal-fast
fun render(): Boolean {
	...	
	GL20C.glUseProgram(program)
	MemoryStack.stackPush().use {
		GL20C.glUniformMatrix4fv(uniformMVP, 1, false, mvp toBuffer it)
	}
	GL30C.glBindVertexArray(vertexArray)		
	GL11C.glDrawElements(GL11C.GL_TRIANGLES, elements.rem, GL11C.GL_UNSIGNED_INT, 0L)	
	GL30C.glBindVertexArray(0)
	GL20C.glUseProgram(0)

	return true
}

fun render(): Boolean {
	...	
	program.used {
		glUniform(uniformMVP, mvp)
		vertexArray.bound {
			glDrawElements(elements)
		}
	}

	return true
}
```

@[1-13]
@[2]
@[3]
@[4-6]
@[7]
@[8]
@[7,9]
@[3,10]
@[12]
@[1-2,15-16]
@[3,10,17,22]
@[4-6, 18]
@[7,9, 19,21]
@[8, 20]
@[12, 24]

---

```kotlin code-reveal-fast

fun end(): Boolean {

	GL15C.glDeleteBuffers(buffers)
	GL20C.glDeleteProgram(program)
	GL30C.glDeleteVertexArrays(vertexArray)

	return true
}

fun end(): Boolean {

	buffers.delete()
	program.delete()
	vertexArray.delete()

	return true
}
```
@[1-8]
@[4, 13]
@[5, 14]
@[6, 15]
@[8, 17]

---

#DSA?

---

@snap[north span-30]
![VULKAN](assets/img/vulkan_logo.png)
@snapend

@ul[list-spaced-bullets text-09]
- cross-platform rendering/computing API
- really low-overhead
- built upon Mantle
- released on February 2016
- latest release on January 2020 (1.2)
@ulend

---

### Welcome *VK²*

- inline classes (and "enums")
- inline functions
- object oriented
- functional programming
- convenience overloading ("low") functions
- glm interoperability

```kotlin	zoom-07
fun createInstance(enableValidation: Boolean): Int { // VkResult
	val stack = MemoryStack.stackPush()	// MemoryStack
	val pAppName = stack.UTF8("Hello Triangle") // ByteBuffer
	val pEngineName = stack.UTF8("My Engine") // ByteBuffer
	val appInfo = VkApplicationInfo.callocStack(stack) // VkApplicationInfo
		.sType(VK10.VK_STRUCTURE_TYPE_APPLICATION_INFO)
		.pApplicationName(pAppName)
		.pEngineName(pEngineName)
		.apiVersion(1)
	val requiredInstanceExtensions = glfwGetRequiredInstanceExtensions() // PointerBuffer
	if (requiredInstanceExtensions == null) 
		throw IllegalStateException("failed to find the platform surface extensions.")

	val instanceCreateInfo = VkInstanceCreateInfo.callocStack(stack)
		.sType(VK10.VK_STRUCTURE_TYPE_INSTANCE_CREATE_INFO)
		.pApplicationInfo(appInfo)
		
	if (requiredInstanceExtensions.isNotEmpty())
		if (settings.validation) {
			val instanceExtensions = stack.callocPointer(requiredInstanceExtensions.size + 1)
			for(i in requiredInstanceExtensions.indices)
				instanceExtensions[i] = requiredInstanceExtensions[i]
			val pVK_EXT_debug_utils = stack.ASCII("VK_EXT_debug_utils")
			instanceExtensions[requiredInstanceExtensions.size] = pVK_EXT_debug_utils
			instanceCreateInfo.ppEnabledExtensionNames(instanceExtensions)
		}
		else
			instanceCreateInfo.ppEnabledExtensionNames(requiredInstanceExtensions)
}
```

@[1]
@[2]
@[3-4]
@[3-9]
@[10-12]
@[14-17]
@[18]
@[18-19, 26]
@[18-20, 26]
@[18-22, 26]
@[18-23, 26]
@[18-24, 26]
@[18-25, 26]
@[18-19, 26-28]

---

```kotlin zoom-07
	if (settings.validation) {
		// The VK_LAYER_KHRONOS_validation contains all current validation functionality.
		val validationLayerName = "VK_LAYER_KHRONOS_validation"
		// Check if this layer is available at instance level
		val instanceLayerCount = stack.mallocInt(1) // IntBuffer
		vkEnumerateInstanceLayerProperties(instanceLayerCount, null)
		val instanceLayerProperties = VkLayerProperties.mallocStack(instanceLayerCount[0], stack) 
			// VkLayerProperties.Buffer
		vkEnumerateInstanceLayerProperties(instanceLayerCount, instanceLayerProperties)
		val validationLayerPresent = instanceLayerProperties.any { 
			it.layerNameString() == validationLayerName
		} // Boolean
		if (validationLayerPresent) {
			val ppValidationLayerName = stack.mallocPointer(1) // PointerBuffer
			ppValidationLayerName[0] = stack.ASCII(validationLayerName)
			instanceCreateInfo.ppEnabledLayerNames = validationLayerName
		} else 
			System.err.println("VK_LAYER_KHRONOS_validation not present, validation is disabled")
	}
	val pInstance = stack.mallocPointer(1) // PointerBuffer
	val result = vkCreateInstance(instanceCreateInfo, null, instance) // Int ~VkResult
	instance = VkInstance(pInstance, instanceCreateInfo)
	stack.pop()
	return result
}
```

@[1, 19]
@[1-3, 19]
@[1,4-6, 19]
@[1,7-9, 19]
@[1,10-12, 19]
@[1,13,17,19]
@[1,14-16,19]
@[1,13,17-19]
@[20-21]
@[22]
@[23]
@[24]

--- 

```kotlin code-reveal-fast zoom-07
fun createInstance(enableValidation: Boolean): VkResult { // inline class
	// no stack, no pStrings
	val appInfo = ApplicationInfo("Hello Triangle", "My Engine", 1) // all jvm, no stype
	val instanceExtensions = glfw.requiredInstanceExtensions // one unique ArrayList<String>
	if (instanceExtensions.isEmpty()) // avoid nullability
		throw IllegalStateException("failed to find the platform surface extensions.")

	val instanceCreateInfo = InstanceCreateInfo(applicationInfo = appInfo)
		
	if (instanceExtensions.isNotEmpty()) {
		if (settings.validation) 
			instanceExtensions += "VK_EXT_debug_utils"
		instanceCreateInfo.enabledExtensionNames = instanceExtensions
	}	
	if (settings.validation) {
		// The VK_LAYER_KHRONOS_validation contains all current validation functionality.
		val validationLayerName = "VK_LAYER_KHRONOS_validation"
		// Check if this layer is available at instance level
		val instanceLayerProperties = vk.enumerateInstanceLayerProperties // Array<LayerProperties>
		val validationLayerPresent = instanceLayerProperties.any { 
			it.layerName == validationLayerName // direct field
		} // Boolean
		if (validationLayerPresent)
			instanceCreateInfo.enabledLayerNames = validationLayerName
		else 
			System.err.println("VK_LAYER_KHRONOS_validation not present, validation is disabled")
	}
	instance = Instance(instanceCreateInfo) // no &, direct instantiation, no alloc
}
```

@[1]
@[2-3]
@[4]
@[5-6]
@[8]
@[10, 14]
@[10-11, 14]
@[10-12, 14]
@[10,13, 14]
@[15,27]
@[15, 16-17, 27]
@[15, 18-19, 27]
@[15, 20-22, 27]
@[15, 23, 27]
@[15, 23-24, 27]
@[15, 23,25-26, 27]
@[28]

---

what about VkResult?

---

what about VkResult?

and the stack?
