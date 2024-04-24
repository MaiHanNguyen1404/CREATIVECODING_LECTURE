---
title: Week 7 Lecture 
published_at: 2024-04-17
snippet: Ascii cam
disable_html_sanitization: true
---

```html
<div id="ascii_div"></div>

<script type="module">

    // Get camera stream, with options object
   const stream = await navigator.mediaDevices.getUserMedia ({ 
      audio: false,
      video: true,
      facingMode: `user`,
   })

   // Get video tracks
   const videoTracks = await stream.getVideoTracks ()
   console.log (`Using video device: ${ videoTracks[0].label }`)

   // Make video html element
   const video = document.createElement (`video`)

   // Assign stream to video source
   video.srcObject = stream
   await video.play ()

   // Make canvas html element 
   const cnv = document.createElement (`canvas`)

   // Small size
   cnv.width  = 64

   // With aspect ratio from the video html element
   cnv.height = cnv.width * video.videoHeight / video.videoWidth

   // Grab the ascii div from DOM
   const div = document.getElementById (`ascii_div`)

   // Set font to be monospace
   div.style.fontFamily = `monospace`

   // Center alligned
   div.style.textAlign = `center`

   // Get canvas context
   const ctx = cnv.getContext (`2d`)

   // String of character from dark -> bright
   const chars = "¶Ñ@%&∆∑∫#Wß¥$£√?!†§ºªµ¢çø∂æåπ*™≤≥≈∞~,.…_¬“‘˚`˙"

   // Define a function for animation
   const draw_frame = async () => {

      // Transformation save point
      ctx.save ()

      // Fllip horzizontally
      ctx.scale (-1, 1)

      // Draw image from video onto wrong side
      // 'Webcam' effects - working like a mirror -> flip
      ctx.drawImage (video, -cnv.width, 0, cnv.width, cnv.height)

      // 
      ctx.restore ()

      // Get pixel data
      const pixels = await ctx.getImageData (0, 0, cnv.width, cnv.height).data

      // Start empty string
      let ascii_img = ``

      // Go through the grid (horizontally and vertically)
      // y + 2 because for characters, the height is bigger than the width
      for (let y = 0; y < cnv.height; y += 2) {
         for (let x = 0; x < cnv.width; x++) {

            // Get pixel position
            const i = (y * cnv.width + x) * 4

            // Get rgb values
            const r = pixels[i]
            const g = pixels[i + 1]
            const b = pixels[i + 2]

            // Calculate brightness 
            // 16581376 = 255 * 255 * 255 
            const br = (r * g * b / 16581376) ** 0.1

            // Use brightness to select character
            const char_i = Math.floor (br * chars.length)

            // Add character to ascii string
            ascii_img += chars[char_i]
         }
         // New line (line breaks)
         ascii_img += `\n`
      }
      // add ascii string to innerText of div
      div.innerText = ascii_img

      // wait and then call next frame
      requestAnimationFrame (draw_frame)
   }
   // start recursive animation
   draw_frame ()
</script>

```