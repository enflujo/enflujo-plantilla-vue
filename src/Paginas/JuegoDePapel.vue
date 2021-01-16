<template>
  <main>
    <video id="video" width="928" height="614" autoplay loop muted @click="enVideoClic">
      <source src="https://juancgonzalez.com/labmoviles/juego-de-papel/loop.mp4" type="video/mp4" />
      <!-- <source src="movie.ogg" type="video/ogg"> -->
    </video>

    <div id="opciones">
      <svg id="opcion1" xmlns="http://www.w3.org/2000/svg" viewbox="0 0 928 615" fill="transparent">
        <g>
          <rect id="fondo" width="928" height="615" />
          <polygon
            id="fucsia"
            class="opcion"
            data-color="fucsia"
            points="464 123.19 464 313.06 293.11 270.02 464 123.19"
          />
          <polygon
            id="anaranjado"
            class="opcion"
            data-color="anaranjado"
            points="464 123.19 464 313.06 634.97 266.77 464 123.19"
          />
          <path
            id="verde"
            class="opcion"
            data-color="verde"
            d="M466.7,316.72s3.19,138.51,7.44,149.14S642.05,276,642.05,276Z"
          />
          <polygon
            id="azul"
            class="opcion"
            data-color="azul"
            points="466.7 316.72 291 280.94 473.78 466.27 466.7 316.72"
          />
        </g>
      </svg>

      <svg id="opcion2" xmlns="http://www.w3.org/2000/svg" viewbox="0 0 929 615" fill="transparent">
        <g>
          <rect class="fondo" width="929" height="615" />
          <polygon
            id="rosado"
            class="opcion"
            data-color="rosado"
            points="447.54 142.76 464.5 346.27 306.73 317.04 447.54 142.76"
          />
          <path
            id="rojo"
            class="opcion"
            data-color="rojo"
            d="M450.73,142.76,466.67,350s139.75-53.14,145.06-47.29S450.73,142.76,450.73,142.76Z"
          />
          <path
            id="amarillo"
            class="opcion"
            data-color="amarillo"
            d="M464.5,349.46s136.07-52.08,147.23-46.76S466.67,493.46,466.67,493.46Z"
          />
          <polygon
            id="morado"
            class="opcion"
            data-color="morado"
            points="464.5 349.46 306.73 318.11 464.5 493.46 464.5 349.46"
          />
        </g>
      </svg>
    </div>

    <div id="resultados"></div>
  </main>
</template>

<script>
export default {
  data() {
    return {
      reproduciendo: false,
      imgsResultados: []
    };
  },
  methods: {
    enVideoClic() {
      if (this.reproduciendo) {
        const tiempo = video.currentTime;

        if (tiempo < 0.5) {
          // Esta al inicio del video y esta cerrado
        } else if (tiempo >= 0.5 && tiempo < 1) {
          // Esta abierto con el primer grupo de colores
          video.pause();
          opcion1.style.display = 'block';
        } else if (tiempo >= 1 && tiempo < 1.5) {
          // Esta cerrado de nuevo
        } else if (tiempo >= 1.5 && tiempo < 2) {
          // Esta abierto en el segundo grupo de colores
          video.pause();
          opcion2.style.display = 'block';
        } else {
          // Esta cerrado de nuevo
        }
      } else {
        video.play();
        this.reproduciendo = true;
      }
    },

    escalarAPantalla() {
      const dimsVideo = [928, 614];
      const zoom = Math.min(window.innerWidth / dimsVideo[0], window.innerHeight / dimsVideo[1]);

      const ancho = dimsVideo[0] * zoom;
      const alto = dimsVideo[1] * zoom;
      const css = {
        width: `${ancho}px`,
        height: `${alto}px`,
        top: '50%',
        left: '50%',
        transform: 'translate(-50%, -50%)'
      };

      Object.assign(video.style, css);
      Object.assign(opcion1.style, css);
      Object.assign(opcion2.style, css);
      Object.assign(resultadosContenedor.style, css);
    }
  },

  mounted() {
    const video = document.getElementById('video');
    const resultadosContenedor = document.getElementById('resultados');
    const opcion1 = document.getElementById('opcion1');
    const opcion2 = document.getElementById('opcion2');
    const opciones = document.querySelectorAll('.opcion');
    const url = 'https://juancgonzalez.com/labmoviles/juego-de-papel/resultados';
    const resultados = {
      fucsia: {},
      anaranjado: {},
      verde: {},
      azul: {},
      rosado: {},
      rojo: {},
      amarillo: {},
      morado: {}
    };
    const numeroResultados = Object.keys(resultados).length;
    let reproduciendo = false;
    let elementosCargados = 0;

    function cargador(e) {
      resultadosContenedor.appendChild(e.target);
      elementosCargados++;

      if (elementosCargados === numeroResultados) {
        this.escalarAPantalla();
      }
    }

    for (let color in resultados) {
      const img = new Image();
      img.id = color;
      img.className = 'resultado';
      img.onload = cargador;
      img.src = `${url}/${color}.jpg`;
      img.onclick = () => {
        img.style.display = 'none';
        video.play();
      };

      resultados[color].img = img;
    }

    opciones.forEach(opcion => {
      opcion.onclick = () => {
        opcion1.style.display = 'none';
        opcion2.style.display = 'none';
        resultados[opcion.dataset.color].img.style.display = 'block';
      };
    });

    video.ontimeupdate = function pointers() {
      const tiempo = video.currentTime;
      if (reproduciendo) {
        if (tiempo < 0.5) {
          console.log('working');
          video.style.cursor = 'default';
          // Esta al inicio del video y esta cerrado
        } else if (tiempo >= 0.5 && tiempo < 1) {
          // Esta abierto con el primer grupo de colores
          video.style.cursor = 'pointer';
        } else if (tiempo >= 1 && tiempo < 1.6) {
          video.style.cursor = 'default';
          // Esta cerrado de nuevo
        } else if (tiempo >= 1.6 && tiempo < 2) {
          // Esta abierto en el segundo grupo de colores
          video.style.cursor = 'pointer';
        } else {
          video.style.cursor = 'default';
          // Esta cerrado de nuevo
        }
      }
    };

    window.onresize = this.escalarAPantalla;
  }
};
</script>

<style lang="scss" scoped>
#video {
  position: absolute;
  z-index: 1;
}

#opcion1,
#opcion2 {
  position: absolute;
  z-index: 2;
  display: none;
}

.opcion {
  cursor: pointer;
}

#resultados {
  position: absolute;
  z-index: 3;
  overflow: hidden;
  pointer-events: none;
}

.resultado {
  width: 100%;
  height: auto;
  display: none;
  pointer-events: auto;
}

/* SVG */
.fondo,
.opcion {
  fill: transparent;
}
</style>
