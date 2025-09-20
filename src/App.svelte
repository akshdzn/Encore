<script>
  import { onMount } from "svelte";

  // mode determination
  let currentMode = 1;
  let encodeArea;
  let decodeArea;

  function setMode(mode) {
    if (mode == 1) {
      currentMode = 1;
    } else {
      currentMode = 0;
    }
    console.log(currentMode);
  }

  // Caesar Cipher
  let cipherCode;

  function caesarCipher(str, shift) {
    // Ensure shift is between 0–25
    shift = shift % 26;

    return str
      .split("")
      .map((char) => {
        let code = char.charCodeAt(0);

        // Uppercase A–Z
        if (code >= 65 && code <= 90) {
          return String.fromCharCode(((code - 65 + shift + 26) % 26) + 65);
        }
        // Lowercase a–z
        else if (code >= 97 && code <= 122) {
          return String.fromCharCode(((code - 97 + shift + 26) % 26) + 97);
        }
        // symbols
        else {
          return char;
        }
      })
      .join("");
  }
</script>

<main>
  <div class="nav">
    <!-- title -->
    <div class="titlebx">
      <div class="title">
        <div class="text-f">ENCORE</div>
        <img src="/assets/shape.svg" alt="encore logo" />
      </div>
      <button aria-label="About">GITHUB</button>
    </div>
    <!-- tools -->
    <div class="toolbx">
      <div class="option">
        <div class="text-f">SHIFT</div>
        <input type="number" min="1" value={currentMode} />
      </div>
      <div class="option">
        <div class="text-f">CIPHER</div>
        <button class="optionBtn">Caesar</button>
      </div>
      <button aria-label="Copy">COPY</button>
    </div>
  </div>
  <div class="content">
    <div class="text-box">
      <textarea
        name="text"
        class="text"
        id="encode"
        on:click={() => {
          setMode(1);
        }}
      ></textarea>
    </div>
    <div class="text-box">
      <textarea
        name="text"
        class="text"
        id="decode"
        on:click={() => {
          setMode(0);
        }}
      ></textarea>
    </div>
  </div>
</main>
