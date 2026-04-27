# Técnicas Avançadas de UI/UX

## Parallax Avançado

```js
// Multi-layer parallax com GSAP
function initParallax() {
  const layers = gsap.utils.toArray("[data-parallax]");

  layers.forEach(layer => {
    const speed = parseFloat(layer.dataset.parallax) || 0.5;
    gsap.to(layer, {
      yPercent: -100 * speed,
      ease: "none",
      scrollTrigger: {
        trigger: layer.closest("section"),
        start: "top bottom",
        end: "bottom top",
        scrub: true,
      }
    });
  });
}
```

## Page Transitions

```jsx
// Com Framer Motion — Layout animations
const pageVariants = {
  initial: { opacity: 0, filter: "blur(8px)", scale: 0.98 },
  enter:   { opacity: 1, filter: "blur(0px)", scale: 1,
             transition: { duration: 0.6, ease: [0.25, 0.1, 0.25, 1] } },
  exit:    { opacity: 0, filter: "blur(8px)", scale: 1.02,
             transition: { duration: 0.4 } },
};

// Com GSAP — transição de páginas
async function pageTransition(onComplete) {
  await gsap.to(".page-overlay", {
    scaleY: 1,
    transformOrigin: "bottom",
    duration: 0.5,
    ease: "power4.inOut"
  });
  onComplete();
  await gsap.to(".page-overlay", {
    scaleY: 0,
    transformOrigin: "top",
    duration: 0.5,
    ease: "power4.inOut"
  });
}
```

## Shader Effects (Three.js)

```glsl
/* Fragment shader — distorção ondulante */
uniform float uTime;
uniform sampler2D uTexture;
varying vec2 vUv;

void main() {
  vec2 uv = vUv;
  float wave = sin(uv.x * 10.0 + uTime) * 0.02;
  uv.y += wave;
  gl_FragColor = texture2D(uTexture, uv);
}
```

## Infinite Marquee

```css
.marquee-track {
  display: flex;
  gap: 2rem;
  animation: marquee 20s linear infinite;
  width: max-content;
}
.marquee-track:hover { animation-play-state: paused; }

@keyframes marquee {
  from { transform: translateX(0); }
  to   { transform: translateX(-50%); }
}
```

## Morphing SVG

```js
// GSAP MorphSVG Plugin
gsap.to("#shape", {
  morphSVG: "#targetShape",
  duration: 1.5,
  ease: "power2.inOut",
  repeat: -1,
  yoyo: true,
});
```

## Scroll Velocity — Skew Effect

```js
let velocityY = 0;
let lastScrollY = 0;

window.addEventListener("scroll", () => {
  velocityY = window.scrollY - lastScrollY;
  lastScrollY = window.scrollY;

  gsap.to(".skew-element", {
    skewY: velocityY * 0.05,
    ease: "power3.out",
    duration: 0.6,
  });
});
```

## Video Background Otimizado

```html
<video
  autoplay
  loop
  muted
  playsinline
  preload="metadata"
  poster="poster.webp"
>
  <source src="video.webm" type="video/webm">
  <source src="video.mp4"  type="video/mp4">
</video>
```

```css
.video-bg {
  position: absolute;
  inset: 0;
  width: 100%;
  height: 100%;
  object-fit: cover;
  z-index: -1;
}
```

## Lottie Animations

```jsx
import Lottie from "lottie-react";

function AnimatedIcon({ animData, trigger }) {
  const lottieRef = useRef(null);

  const handleMouseEnter = () => lottieRef.current?.play();
  const handleMouseLeave = () => lottieRef.current?.stop();

  return (
    <div onMouseEnter={handleMouseEnter} onMouseLeave={handleMouseLeave}>
      <Lottie
        lottieRef={lottieRef}
        animationData={animData}
        autoplay={false}
        loop={false}
        style={{ width: 48, height: 48 }}
      />
    </div>
  );
}
```

## Split Text Reveal Avançado

```js
function revealOnScroll(selector) {
  const elements = gsap.utils.toArray(selector);

  elements.forEach(el => {
    const split = new SplitText(el, { type: "lines" });

    // Wrap lines para overflow hidden
    split.lines.forEach(line => {
      const wrapper = document.createElement("div");
      wrapper.style.overflow = "hidden";
      line.parentNode.insertBefore(wrapper, line);
      wrapper.appendChild(line);
    });

    gsap.from(split.lines, {
      y: "110%",
      opacity: 0,
      duration: 0.9,
      stagger: 0.08,
      ease: "power4.out",
      scrollTrigger: {
        trigger: el,
        start: "top 85%",
        once: true,
      }
    });
  });
}
```

## Floating Elements (idle animation)

```css
@keyframes float {
  0%, 100% { transform: translateY(0px) rotate(0deg); }
  33%       { transform: translateY(-12px) rotate(2deg); }
  66%       { transform: translateY(-6px) rotate(-1deg); }
}

.floating {
  animation: float 6s ease-in-out infinite;
}
.floating-delayed {
  animation: float 6s ease-in-out 2s infinite;
}
```

## Image Hover 3D Tilt

```js
function init3DTilt(element) {
  element.addEventListener("mousemove", (e) => {
    const rect = element.getBoundingClientRect();
    const x = (e.clientX - rect.left) / rect.width  - 0.5;
    const y = (e.clientY - rect.top)  / rect.height - 0.5;

    gsap.to(element, {
      rotateX: -y * 15,
      rotateY:  x * 15,
      transformPerspective: 1000,
      ease: "power2.out",
      duration: 0.3,
    });
  });

  element.addEventListener("mouseleave", () => {
    gsap.to(element, {
      rotateX: 0,
      rotateY: 0,
      duration: 0.6,
      ease: "elastic.out(1, 0.5)",
    });
  });
}
```
