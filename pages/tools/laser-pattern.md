---
title: Laser pattern generator
tags: []
slug: /tools/laser-pattern
navbar_path: []
---
<div>
  <h1>Laser cutter grid generator</h1>
  <p>A simple tool for generating grids for laser cutting..</p>
  <noscript>
    Sorry, but your browser appears to not support JavaScript. Please upgrade to
    Internet Explorer 3.0 or Netscape Navigator 2.0 to use this applet.
  </noscript>
  <form id="generator-input">
    <div>
      <div>
        Units
        <select class="generator-units" name="length-scaling">
          <option value="1" checked>mm</option>
          <option value="10">cm</option>
          <option value="1000">m</option>
          <option value="25.4">in</option>
          <option value="304.8">ft</option>
        </select>
      </div>
      <div class="sheet-params">
        <h2>Sheet parameters</h2>
        <p>(custom shapes besides rounded rectangles Coming Soon™)</p>
        <div>
          <label>
            Width
            <input
              class="shared-units"
              name="sheet-width"
              type="number"
              value="300"
            />
          </label>
          <label>
            Horizontal padding
            <input
              class="shared-units"
              name="sheet-h-padding"
              type="number"
              value="10"
            />
          </label>
        </div>
        <div>
          <label>
            Height
            <input
              class="shared-units"
              name="sheet-height"
              type="number"
              value="420"
            />
          </label>
          <label>
            Vertical padding
            <input
              class="shared-units"
              name="sheet-v-padding"
              type="number"
              value="10"
            />
          </label>
        </div>
        <div>
          <label>
            Corner fillet radius
            <input
              class="shared-units"
              name="sheet-fillet-radius"
              type="number"
              value="20"
            />
          </label>
        </div>
      </div>
      <div class="grid-params">
        <h2>Grid parameters</h2>
        <div>
          Horizontal parity
          <label>
            <input name="sheet-h-parity" type="radio" value="auto" checked />
            Auto
          </label>
          <label>
            <input name="sheet-h-parity" type="radio" value="even" /> Even
          </label>
          <label>
            <input name="sheet-h-parity" type="radio" value="odd" /> Odd
          </label>
        </div>
        <div>
          Vertical parity
          <label>
            <input name="sheet-v-parity" type="radio" value="auto" checked />
            Auto
          </label>
          <label>
            <input name="sheet-v-parity" type="radio" value="even" /> Even
          </label>
          <label>
            <input name="sheet-v-parity" type="radio" value="odd" /> Odd
          </label>
        </div>
        <label>
          Grid pattern
          <select name="grid-pattern">
            <option value="std-pegboard">1" pegboard with ¼" holes</option>
            <option value="skadis">IKEA SKÅDIS Pegboard</option>
            <!--option value="wall-control">
                  1" pegboard with ¼" holes and 1" slots (Wall Control style)
                </option-->
            <option value="molle-half">MOLLE (60% holes)</option>
            <option value="molle-full">MOLLE (90% dual holes)</option>
          </select>
        </label>
      </div>
    </div>
    <input type="hidden" name="debug-mode" id="debug-mode" value="1" />
  </form>
  <div id="generator-output" style="margin: 20px">
    <h2>Output</h2>
    <p id="summary"></p>
    <div>
      <button onclick="doSVGDownload()">Download SVG</button>
    </div>
    <div
      style="
        margin-left: auto;
        margin-right: auto;
        max-width: 500px;
        max-height: 500px;
      "
    >
      <svg
        style="width: 100%; height: 100%; max-width: 500px; max-height: 500px"
      >
        <style>
          * {
            fill: transparent;
            stroke-width: 1px;
          }
        </style>
        <rect class="sheet" stroke="black" />
        <g class="holes"></g>
      </svg>
    </div>
  </div>
</div>
<div hidden>
  <svg
    id="template-std-pegboard"
    width="2.54cm"
    height="2.54cm"
    viewBox="0 0 25.4 25.4"
  >
    <circle cx="12.7" cy="12.7" r="3.175" stroke="red" stroke-width="1px" />
  </svg>
  <svg id="template-skadis-1" width="2cm" height="4cm" viewBox="0 0 20 40">
    <g transform="translate(10 0)">
      <path
        d="M 2.5 5
           v 10
           a 2.5 2.5 0 0 1 -5 0
           v -10
           a 2.5 2.5 0 0 1 5 0"
        stroke="red"
        stroke-width="1px"
      />
    </g>
  </svg>
  <svg id="template-skadis-2" width="2cm" height="4cm" viewBox="0 0 20 40">
    <g transform=" translate(10 20)">
      <path
        d="M 2.5 5
           v 10
           a 2.5 2.5 0 0 1 -5 0
           v -10
           a 2.5 2.5 0 0 1 5 0"
        stroke="red"
        stroke-width="1px"
      />
    </g>
  </svg>
  <svg id="template-molle-half" width="3.8cm" height="5cm" viewBox="0 0 38 50">
    <rect
      x="2.5"
      y="2.5"
      width="35"
      height="30"
      rx="5"
      stroke="red"
      stroke-width="1px"
    />
  </svg>
  <svg
    id="template-molle-full"
    width="3.8cm"
    height="2.5cm"
    viewBox="0 0 38 25"
  >
    <rect
      x="2.5"
      y="2.5"
      width="35"
      height="22"
      rx="5"
      stroke="red"
      stroke-width="1px"
    />
  </svg>
</div>
<script>
  const PATTERNS = {
    "std-pegboard": {
      w: 25.4,
      h: 25.4,
      tess: [[document.getElementById("template-std-pegboard")]],
    },
    skadis: {
      w: 20,
      h: 40,
      tess: [
        [
          document.getElementById("template-skadis-1"),
          document.getElementById("template-skadis-2"),
        ],
      ],
    },
    "molle-half": {
      w: 38,
      h: 50,
      tess: [[document.getElementById("template-molle-half")]],
    },
    "molle-full": {
      w: 38,
      h: 25,
      tess: [[document.getElementById("template-molle-full")]],
    },
  };
  /** Holder for everything */
  class LaserPatternGenerator {
    constructor({ form, svg }) {
      this.form = form;
      this.svg = svg;
      const inputs = form.querySelectorAll(
        ".sheet-params input, .sheet-params input, .grid-params input, .grid-params select"
      );
      console.debug("hooking input event on form inputs", inputs);
      inputs.forEach((e) =>
        e.addEventListener("input", () => {
          this.updateSVG();
        })
      );
      console.debug("hooking change event for unit selector", inputs);
      const units = form.querySelector(".generator-units");
      var prevUnitScale = Number(units.value);
      units.addEventListener("change", (ev) => {
        const newUnitScale = Number(units.value);
        console.debug(
          "performing units change from",
          prevUnitScale,
          "to",
          newUnitScale
        );
        this.updateUnits(prevUnitScale / newUnitScale);
        prevUnitScale = newUnitScale;
      });
    }
    updateUnits(multiplier) {
      this.form.querySelectorAll(".shared-units").forEach((e) => {
        const rawVal = Number(e.value) * multiplier;
        const roundTo = 1000;
        e.value = Math.round((rawVal + Number.EPSILON) * roundTo) / roundTo;
      });
    }
    updateSVG() {
      const formData = new FormData(this.form);
      const holes = this.svg.querySelector(".holes");
      const rect = this.svg.querySelector(".sheet");
      const scaling = Number(formData.get("length-scaling"));
      const params = {
        debug: formData.get("debug-mode") == "1",
        w: Number(formData.get("sheet-width")) * scaling,
        xpad: Number(formData.get("sheet-h-padding")) * scaling,
        xpar: formData.get("sheet-h-parity"),
        h: Number(formData.get("sheet-height")) * scaling,
        ypad: Number(formData.get("sheet-v-padding")) * scaling,
        ypar: formData.get("sheet-v-parity"),
        fillet: Number(formData.get("sheet-fillet-radius")) * scaling,
        pattern: PATTERNS[formData.get("grid-pattern")],
      };
      console.debug("Rendering SVG with params", params);
      // resize the sheet
      rect.setAttribute("x", 0);
      rect.setAttribute("y", 0);
      rect.setAttribute("width", params.w);
      rect.setAttribute("height", params.h);
      rect.setAttribute("rx", params.fillet);
      rect.setAttribute("ry", params.fillet);
      this.svg.setAttribute("width", `${params.w / 10}cm`);
      this.svg.setAttribute("height", `${params.h / 10}cm`);
      this.svg.setAttribute("viewBox", `0 0 ${params.w} ${params.h}`);
      // clean the old holes
      holes.replaceChildren();
      // calculate the grid
      const ptn = params.pattern;
      const xGrid = packAxis(params.w, ptn.w, params.xpar, params.xpad);
      const yGrid = packAxis(params.h, ptn.h, params.ypar, params.ypad);
      console.debug("Calculated grid axes", xGrid, yGrid);
      // put in the new holes
      for (let i = 0; i < xGrid.count; i++) {
        for (let j = 0; j < yGrid.count; j++) {
          const x = xGrid.step * i + xGrid.offset;
          const y = yGrid.step * j + yGrid.offset;
          const xrepeat = ptn.tess[0].length;
          const yrepeat = ptn.tess.length;
          const tesspattern = ptn.tess[j % yrepeat][i % xrepeat];
          const group = document.createElementNS(
            "http://www.w3.org/2000/svg",
            "g"
          );
          group.setAttribute("transform", `translate(${x} ${y})`);
          holes.appendChild(group);
          tesspattern.childNodes.forEach((child) => {
            group.appendChild(child.cloneNode(true));
          });
        }
      }
    }
  }
  function packAxis(lenLimit, lenCell, parity, padding) {
    var count = Math.floor((lenLimit - 2 * padding) / lenCell);
    switch (parity) {
      case "auto":
        break;
      case "odd":
        if (count % 2 == 0) {
          count--;
        }
        break;
      case "even":
        if (count % 2 == 1) {
          count--;
        }
        break;
      default:
        throw new TypeError(`unknown parity ${parity}`);
    }
    return {
      count,
      offset: (lenLimit - count * lenCell) / 2,
      step: lenCell,
    };
  }
  const laserPatternGenerator = new LaserPatternGenerator({
    form: document.querySelector("#generator-input"),
    svg: document.querySelector("#generator-output svg"),
  });
  laserPatternGenerator.updateSVG();
  function doSVGDownload() {
    const data = laserPatternGenerator.svg.outerHTML;
    var blob = new Blob([data], { type: "image/svg+xml;charset=utf-8" });
    var url = URL.createObjectURL(blob);
    var downloadLink = document.createElement("a");
    downloadLink.href = url;
    downloadLink.download = "pattern.svg";
    document.body.appendChild(downloadLink);
    downloadLink.click();
    document.body.removeChild(downloadLink);
  }
</script>