<template>
    <Container ref="container">

        <!-- Background -->
        <div>
            <img src="/public/images/02_coffee_elements/bg-01.png" class="max-w-screen max-h-screen object-contain">
        </div>

        <!-- Circle Left -->
        <div class="absolute top-[0.5%] left-[-11.5%] w-[35%] circle-move">
            <img src="/public/images/02_coffee_elements/ciecle left-04.png">
        </div>

        <!-- Circle Right -->
        <div class="absolute top-[-3%] right-[5%] w-[35%] circle-move">
            <img src="/public/images/02_coffee_elements/circle-04.png">
        </div>

        <div class="absolute top-[1%] right-[-10%] w-[35%] circle-move">
            <img src="/public/images/02_coffee_elements/circle-04.png">
        </div>

        <!-- Menu Frame -->
        <div class="absolute top-[-5%] left-[0%] w-[100%]">
            <img src="/public/images/02_coffee_elements/frame-02.png">
        </div>
        
        <!-- Coffee Glass -->
        <div class="absolute top-[36%] left-[2%] w-[35%]">
            <img src="/public/images/02_coffee_elements/glass-04.png">
        </div>

        <!-- Home Icon -->
        <div class="absolute top-[2%] left-[2%] w-[7%]">
            <NuxtLink to="/">
                <img src="/public/images/general/home-icon.png">
            </NuxtLink>
        </div>
        
        <!-- Info Icon -->
        <div @click="showPopUp" class="absolute top-[2%] left-[91%] w-[7%]">
            <img src="/public/images/02_coffee_elements/icpn_-04.png">
        </div>

        <!-- ML Frame -->
        <div class="absolute top-[-12%] left-[6%] w-[42%]">
            <img src="/public/images/02_coffee_elements/frame ml-04.png">
        </div>

        <!-- Coffee Mix -->
        <div class="absolute top-[35%] left-[33.5%] w-[35%] z-10">
            <img src="/public/images/02_coffee_elements/coffee-04.png">
        </div>

        <!-- Coffee Pour -->
        <div ref="coffeePour" class="absolute top-[38%] left-[31.5%] w-[35%] opacity-0">
            <img src="/public/images/02_coffee_elements/coffee3-04.png">
        </div>

        <!-- Water -->
        <img ref="water" src="/public/images/02_coffee_elements/water w-03.png" class="absolute w-[10%] opacity-0" :style="{ top: `${waterPos.top}%`, left: `${waterPos.left}%` }">

        <!-- Coffee Filter -->
        <div class="absolute top-[17.6%] left-[34.75%] w-[35%]">
            <img src="/public/images/02_coffee_elements/coffee2-04.png">
        </div>

        <!-- Coffee Mask -->
        <div class="mask-coffee absolute top-[62%] left-[37.75%] w-[25%]">
            <img ref="coffeeFill" class="w-[100%]" src="/public/images/02_coffee_elements/coffeefill-04.png" :style=" { transform: `translateY(${fill}%)` }">
        </div>

        <!-- Pot -->
        <div ref="pot" @mousedown="startDrag" @touchstart="startDrag" 
            class="absolute w-[28%]" 
            :style="{ top: `${pos.top}%`, left: `${pos.left}%`, transform: `rotate(${potRotation}deg)` }"
        >
            <img src="/public/images/02_coffee_elements/pot-03.png">
        </div>

        <!-- Coffee ML Text -->
        <div class="absolute flex top-[21.5%] left-[17.5%] w-[15%]">
            <div class="text-center w-full">
                <p class="ml-text text-responsive text-white">
                    {{ coffeeVolume }} ML
                </p>
            </div>
        </div>

        <!-- Perfect Pop-up -->
        <div ref="perfectBg" class="absolute flex w-[100%] h-[100%] top-0 left-0 z-20 justify-center pointer-events-none opacity-0 bg-black bg-opacity-25">
            <img ref="perfectPopup" class="pointer-events-none" style="transform: scale(0);" src="/public/images/02_coffee_elements/perfect popup-03.png">
        </div>

    </Container>
</template>


<script setup>

let coffeeVolume = ref(0);
const pot = ref(null);
const water = ref(null);
const coffeePour = ref(null)
const container = ref(null);
const coffeeFill = ref(null)
const perfectBg = ref(null)
const perfectPopup = ref(null)


let fill = ref(100)
let pos = ref({ top: 48, left: 65, pcx: 0, pcy: 0 });
let waterPos = ref({ top: 71, left: 57 });
let potRotation = ref(0);


let coffeeInterval = null;

let containerBounds = null;

function getBound() {
    const containerElement = container.value?.$el || container.value;
    container.value = containerElement.getBoundingClientRect();
    containerBounds = containerElement.getBoundingClientRect();
}

function startDrag(e) {
    e.preventDefault();

    containerBounds == null ? getBound() : console.log('gotten');

    if (coffeeVolume.value < 200) {
        const clientX = e.touches ? e.touches[0].clientX : e.clientX;
        const clientY = e.touches ? e.touches[0].clientY : e.clientY;

        const pcX = (clientX - container.value.left) / container.value.width * 100;
        const pcY = (clientY - container.value.top) / container.value.height * 100;

        pos.value.left = pcX - 15
        pos.value.top = pcY - 30

        pos.value.pcx = pcX
        pos.value.pcy = pcY

        document.addEventListener('mousemove', onDrag);
        document.addEventListener('touchmove', onDrag);
        document.addEventListener('mouseup', stopDrag);
        document.addEventListener('touchend', stopDrag);
    }
    else {
        console.log('stop')
    }

}

function onDrag(e) {
    const clientX = e.touches ? e.touches[0].clientX : e.clientX;
    const clientY = e.touches ? e.touches[0].clientY : e.clientY;

    const dpcX = (clientX - container.value.left) / container.value.width * 100;
    const dpcY = (clientY - container.value.top) / container.value.height * 100;

    pos.value.left = dpcX - 15
    pos.value.top = dpcY - 30

    waterPos.value.left = dpcX - 23
    waterPos.value.top = dpcY - 8.5

    if (pos.value.left <= 61 && pos.value.left >= 52 && pos.value.top <= 12) {
        pot.value.style.transition = 'transform 0.1s ease';
        potRotation = -38
        water.value.style.opacity = 1
        water.value.style.height = (20 - pos.value.top) + '%'

        pourCoffee()
    }
    else {
        pot.value.style.transition = 'transform 0.1s ease';
        potRotation = 0
        water.value.style.opacity = 0

        stopPouring()
    }

}

function stopDrag() {
    pos.value.left = 65
    pos.value.top = 48

    waterPos.value.top = 71
    waterPos.value.left = 57
    water.value.style.opacity = 0

    pot.value.style.transition = 'none';
    potRotation = 0

    stopPouring()

    document.removeEventListener('mousemove', onDrag);
    document.removeEventListener('touchmove', onDrag);
    document.removeEventListener('mouseup', stopDrag);
    document.removeEventListener('touchend', stopDrag);
}

function pourCoffee() {
  if (coffeeInterval) return;

  coffeeInterval = setInterval(() => {
    coffeeVolume.value += 1;
    fill.value = 100 - coffeeVolume.value / 2;
    if (coffeeVolume.value >= 200) {
        stopDrag()
        ShowPerfectPopup()
    }

  }, 70);

  coffeePour.value.style.opacity = 1;
  coffeePour.value.classList.add('pouring');
}

function stopPouring() {
  if (coffeeInterval) {
    clearInterval(coffeeInterval);
    coffeeInterval = null;
  }

  coffeePour.value.style.opacity = 0;
  coffeePour.value.classList.remove('pouring');
}

function ShowPerfectPopup() {
    perfectBg.value.classList.add('perfect-bg-fade')
    perfectPopup.value.classList.add('perfect-pop')
}


</script>


<style scoped>
.ml-text {
    font-family: 'wintermouse', sans-serif;
}

.text-responsive {
    font-size: 7vh;
}

@media (max-aspect-ratio: 16/9) {

.text-responsive {
    font-size: 3.9375vw;
}

}

.mask-coffee {
    -webkit-mask-image: url(/public/images/02_coffee_elements/coffeemask-04.png);
    mask-image: url(/public/images/02_coffee_elements/coffeemask-04.png);
    mask-repeat: no-repeat;
    -webkit-mask-position: center;
    mask-position: center;
    -webkit-mask-size: contain;
    mask-size: contain;
}

@keyframes pourAnimation {
  0% {
    transform: scaleX(0.85);
  }
  100% {
    transform: scaleX(1);
  }
}

@keyframes circleAnimation {
    0% {
        rotate: 2deg;
    }
    100% {
        rotate: -2deg;
    }
}

@keyframes perfectOpacity {
    0% {
        opacity: 0;
    }
    100% {
        opacity: 100;
    }
}

@keyframes perfectScale {
    0% {
        transform: scale(0);
        opacity: 0;
    }
    100% {
        transform: scale(1);
        opacity: 1;
    }
}

.pouring {
    animation: pourAnimation 1s infinite alternate linear;
    transform-origin: center;
}

.circle-move {
    animation: circleAnimation 0.6s infinite alternate ease-in-out;
    transform-origin: 50% 0%;
}

.perfect-pop {
    animation: perfectScale 2s normal ease;
    animation-fill-mode: forwards;
    transform-origin: center;
}

.perfect-bg-fade {
    animation: perfectOpacity 1s normal ease;
    animation-fill-mode: forwards;
}

</style>
