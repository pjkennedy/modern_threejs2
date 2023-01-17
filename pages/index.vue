<template>
<div id="container" class="absolute text-white text-center w-full px-6 max-w-2xl" style="
          top: 50%; width: 100%;
          transform: translateY(-50%, -50%); left: 5%;
          ">

        <h1 id="patrickKennedy"
        class="font-space-mono text-sm uppercase
        tracking-wide opacity-0"
        style="transform: translateY(30px)">Patrick Kennedy</h1>
        <p id="oneWithAn" class="font-exo text-4xl" style="transform: translateY(30px)">
          ONE WITH AN EVERLASTING
          DESIRE FOR THE UNKNOWN & UNTOLD
        </p>
        <a id="viewWorkBtn" href="https://chriscourses.com/"
          class="border px-4 py-2 rounded-lg text-sm font-space-mono uppercase mt-8
            hover:bg-white hover:text-gray-800 inline-block
            opacity-0" style="transform:
            translateY(30px)">
            View Work</a>
      </div>
</template>


<script>
import gsap from 'gsap'

import { BufferAttribute,
  BufferGeometry,
  DirectionalLight,
  DoubleSide,
  FlatShading,
  Float32BufferAttribute,
  Mesh,
  MeshPhongMaterial,
  PerspectiveCamera,
  PlaneGeometry,
  Points,
  PointsMaterial,
  Raycaster,
  Scene,
  WebGLRenderer
} from 'three'

/*
  npm notes that trhere are 15 high severity vulnerabilities;
  this is due to orbit-controls using an older version of
  three apparently
*/
import OrbitControls from 'orbit-controls-es6';

export default {
  mounted() {
    const dat = require('dat.gui')
    const gui = new dat.GUI()
    const world = {
      plane: {
        width: 400,
        height: 400,
        widthSegments: 50,
        heightSegments: 50
      }
    }
    gui.add(world.plane, 'width', 1, 500).
      onChange(generatePlane)

    gui.add(world.plane, 'height', 1, 500).
      onChange(generatePlane)

    gui.add(world.plane, 'widthSegments', 1, 100).
      onChange(generatePlane)

    gui.add(world.plane, 'heightSegments', 1, 100).
      onChange(generatePlane)

    // not as performant, but got the code to work this way;
    // good for the development cycle anyway...
    let array = []

    function generatePlane() {
      planeMesh.geometry.dispose()
      planeMesh.geometry = new PlaneGeometry
        (world.plane.width,
        world.plane.height,
        world.plane.widthSegments,
        world.plane.heightSegments)


      //breaking issue - originalPostion must be here, vice below
      // with randomValues and array

        // vertice position randomization
        array = planeMesh.geometry.attributes.position.array
        const randomValues = []
        for (let i = 0; i < array.length; i++) {
          if (i % 3 === 0) {
            const x = array[i]
            const y = array[i + 1]
            const z = array[i + 2]

            array[i] = x + (Math.random() - 0.5) * 3
            array[i + 1] = y + (Math.random() - 0.5) * 3
            array[i + 2] = z + (Math.random() - 0.5) * 10
          }

          randomValues.push(Math.random() * Math.PI * 2 )
        }

        planeMesh.geometry.attributes.position.randomValues = randomValues

        planeMesh.geometry.attributes.position.originalPosition = planeMesh.geometry.attributes.position.array

        console.log(planeMesh.geometry.attributes.position)


        const colors = []
        for (let i = 0; i < planeMesh.geometry.attributes.position.count; i++) {
          colors.push(0, 0.19, 0.4)
        }

        planeMesh.geometry.
          setAttribute('color', new BufferAttribute(new
              Float32Array(colors), 3)
          )

    }

    const raycaster = new Raycaster()
    const scene = new Scene();
    const camera = new PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000)
    const renderer = new WebGLRenderer(
                              )

    //console.log(scene);
    //console.log(camera);
    //console.log(renderer);

    renderer.setSize(innerWidth, innerHeight)
    renderer.setPixelRatio(devicePixelRatio)
    document.body.appendChild(renderer.domElement)

    new OrbitControls(camera, renderer.domElement)


    /// removing the yellow box; next two ///
    ///const boxGeometry = new BoxGeometry(1, 1, 1)
    ///const material = new MeshBasicMaterial({ color: 0x00ff00})
    //console.log(boxGeometry)
    //console.log(material)

    ///const mesh = new Mesh( boxGeometry, material)
    //console.log(mesh)
    /// removing the yellow box code ///
    //scene.add(mesh)
    /// end removing the yellow box code ///


    camera.position.z = 50

    const planeGeometry = new PlaneGeometry(
                                    world.plane.width,
                                    world.plane.height,
                                    world.plane.widthSegments,
                                    world.plane.heightSegments)
    const planeMaterial = new MeshPhongMaterial({
      side: DoubleSide,
      flatShading: FlatShading,
      vertexColors: true
    })

    const planeMesh = new Mesh(
      planeGeometry, planeMaterial
    )
    scene.add(planeMesh)
    generatePlane()

    const light = new DirectionalLight(
      0xffffff, 1
    )
    light.position.set(0, -1, 1)
    scene.add(light)


    const backLight = new DirectionalLight(
      0xffffff, 1
    )
    backLight.position.set(0, 0, -1)
    scene.add(backLight)

    const starGeometry = new BufferGeometry()
    const starMaterial = new PointsMaterial({
      color: 0xffffff
    })

    const starVerticies = []
    for (let i = 0; i < 10000; i++) {
      const x = (Math.random() - 0.5) * 2000
      const y = (Math.random() - 0.5) * 2000
      const z = (Math.random() - 0.5) * 2000
      starVerticies.push(x, y, z)

    }

    //console.log(starVerticies)

    starGeometry.setAttribute('position',
      new Float32BufferAttribute(
        starVerticies, 3
      ))


    //console.log(starGeometry)
    //console.log(starMaterial)

    const stars = new Points(starGeometry,
      starMaterial)
    scene.add(stars)

    const mouse = {
      x: undefined,
      y: undefined
    }

    let frame = 0
    function animate() {
      requestAnimationFrame(animate)
      renderer.render(scene, camera)
      raycaster.setFromCamera(mouse, camera)

      frame += 0.01

      // when I moved this from above, it broke

      const {
        /* array, */
        originalPosition,
        randomValues
      } = planeMesh.geometry.attributes.position

      for (let i = 0; i < array.length; i += 3) {
        // x
        array[i] = originalPosition[i] + Math.cos(frame + randomValues[i]) * 0.01

        // y
        array[i + 1] =
          originalPosition[i + 1] + Math.sin(frame + randomValues[i + 1]) * 0.001
      }

      planeMesh.geometry.attributes.position.
        needsUpdate = true

      const intersects = raycaster
        .intersectObject(planeMesh)
      if (intersects.length > 0) {
        // 363 pieces
        //console.log(intersects[0].face)

        //console.log(intersects[0].object.geometry)

        // float32array structed as 121 x 3
        //console.log(intersects[0].object.geometry.attributes.color)

        // setting values; easier to use 1 (as red) to confirm it works
        //intersects[0].object.geometry.attributes.color.setX(0, 1)
        // and intersects[0].face give you the three vertexes, so
        // you can change all three, instead of just one.
        // And setting it zero doesn't work yet, but setting it 1 (for red) does work.
        // Keeping next three to document it
        //intersects[0].object.geometry.attributes.color.setX(intersects[0].face.a, 0)
        //intersects[0].object.geometry.attributes.color.setX(intersects[0].face.b, 0)
        //intersects[0].object.geometry.attributes.color.setX(intersects[0].face.c, 0)

        // shorten above code with color variable:
        /*
        const {color} = intersects[0].object.geometry.attributes
        color.setX(intersects[0].face.a, 0)
        color.setX(intersects[0].face.b, 0)
        color.setX(intersects[0].face.c, 0)  */


        const {color} = intersects[0].object.geometry.attributes

        // also documenting
        /*
        // vertex 1
        color.setX(intersects[0].face.a, 0.1)
        color.setY(intersects[0].face.a, 0.5)
        color.setZ(intersects[0].face.a, 1)
        // vertex 2
        color.setX(intersects[0].face.b, 0.1)
        color.setY(intersects[0].face.b, 0.5)
        color.setZ(intersects[0].face.b, 1)
        //vertex 3
        color.setX(intersects[0].face.c, 0.1)
        color.setY(intersects[0].face.c, 0.5)
        color.setZ(intersects[0].face.a, 1)

        color.needsUpdate = true
        */
        const initialColor = {
          r: 0,
          g: 0.19,
          b: 0.4
        }

        const hoverColor = {
          r: 0.1,
          g: 0.18,  /* instead of the suggested 5 */
          b: 1
        }

        gsap.to(hoverColor, {
          r: initialColor.r,
          g: initialColor.g,
          b:initialColor.b,
          duration: 1,
          onUpdate: () => {
            // vertex 1
            color.setX(intersects[0].face.a, hoverColor.r)
            color.setY(intersects[0].face.a, hoverColor.g)
            color.setZ(intersects[0].face.a, hoverColor.b)
            // vertex 2
            color.setX(intersects[0].face.b, hoverColor.r)
            color.setY(intersects[0].face.b, hoverColor.g)
            color.setZ(intersects[0].face.b, hoverColor.b)
            //vertex 3
            color.setX(intersects[0].face.c, hoverColor.r)
            color.setY(intersects[0].face.c, hoverColor.g)
            color.setZ(intersects[0].face.a, hoverColor.b)

            color.needsUpdate = true

          }
        })

      }

      stars.rotation.x += 0.0005
    }

    renderer.render(scene, camera)

    animate()


    window.addEventListener('mousemove', (e) => {
      mouse.x = (e.clientX / innerWidth) * 2 - 1;
      mouse.y = -(e.clientY / innerHeight) * 2 + 1
      //console.log(mouse)
    })

    gsap.to('#patrickKennedy', {
      opacity: 1,
      duration: 1.5,
      y: 0,
      ease: 'expo'
    })

    gsap.to('#oneWithAn', {
      opacity: 1,
      duration: 1.5,
      delay: 0.3,
      y: 0,
      ease: 'expo'
    })


    gsap.to('#viewWorkBtn', {
      opacity: 1,
      duration: 1.5,
      delay: 0.6,
      y: 0,
      ease: 'expo'
    })

    let viewWork = document.querySelector('#viewWorkBtn')

      viewWork.addEventListener('click', (event) => {
        event.preventDefault()
        //console.log('go')

      gsap.to("#container", {
        opacity: 0
      })
      gsap.to(camera.position, {
        z: 25,
        ease: 'power3.inOut',
        duration: 1.5
      })
      gsap.to(camera.rotation, {
        x: 1.57,
        ease: 'power3.inOut',
        duration: 3
      })
      gsap.to(camera.position, {
        y: 1000,
        ease: 'power3.in',
        duration: 1,
        delay: 2,
        onComplete: () => {
          window.location = 'https://chriscourses.com/'
        }
      })

    })

    addEventListener('resize', () => {
      camera.aspect = innerWidth / innerHeight
      camera.updateProjectionMatrix()
      renderer.setSize(innerWidth,
        innerHeight)


    })


  } // mounted()
} // default
</script>
