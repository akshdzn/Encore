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
    }

    // swtich ciphers
    let cipherIndex = ["Caesar", "base64", "Binary"];
    let currentCipherIndex = 0;
    let currentCipher = cipherIndex[currentCipherIndex];

    function nextCipher() {
        currentCipherIndex = (currentCipherIndex + 1) % cipherIndex.length;
        currentCipher = cipherIndex[currentCipherIndex];
    }

    // encoded and decoded text
    let encodeText = "";
    let decodeText = "";

    // Caesar Cipher
    // show and hide the shift option

    let shift = 1;

    // shift fixer
    function limitShift() {
        if (shift > 25) {
            shift = 25;
        } else if (shift < 1) {
            shift = 1;
        }
    }

    function caesarCipher(str, shift) {
        // Ensure shift is between 0–25
        shift = shift % 26;

        return str
            .split("")
            .map((char) => {
                let code = char.charCodeAt(0);

                // Uppercase A–Z
                if (code >= 65 && code <= 90) {
                    return String.fromCharCode(
                        ((code - 65 + shift + 26) % 26) + 65,
                    );
                }
                // Lowercase a–z
                else if (code >= 97 && code <= 122) {
                    return String.fromCharCode(
                        ((code - 97 + shift + 26) % 26) + 97,
                    );
                }
                // symbols
                else {
                    return char;
                }
            })
            .join("");
    }

    function caesarDecipher(str, shift) {
        // Ensure shift is between 0–25
        shift = shift % 26;

        return str
            .split("")
            .map((char) => {
                let code = char.charCodeAt(0);

                // Uppercase A–Z
                if (code >= 65 && code <= 90) {
                    return String.fromCharCode(
                        ((code - 65 - shift + 26) % 26) + 65,
                    );
                }
                // Lowercase a–z
                else if (code >= 97 && code <= 122) {
                    return String.fromCharCode(
                        ((code - 97 - shift + 26) % 26) + 97,
                    );
                }
                // symbols
                else {
                    return char;
                }
            })
            .join("");
    }

    $: if (currentCipher == "Caesar") {
        if (currentMode === 1) {
            decodeText = caesarCipher(encodeText, shift);
        } else if (currentMode === 0) {
            encodeText = caesarDecipher(decodeText, shift);
        }
    }

    // base64
    function base64Cipher(str) {
        return btoa(unescape(encodeURIComponent(str)));
    }

    function base64Decipher(str) {
        return decodeURIComponent(escape(atob(str)));
    }

    $: if (currentCipher == "base64") {
        if (currentMode == 1) {
            decodeText = base64Cipher(encodeText);
        } else if (currentMode == 0) {
            encodeText = base64Decipher(decodeText);
        }
    }

    // binary
    function textToBinary(text) {
        let binaryArray = [];
        for (let i = 0; i < text.length; i++) {
            // Convert each character to its ASCII value and then to binary
            let binary = text.charCodeAt(i).toString(2);
            // Pad with leading zeros to ensure 8 bits
            binaryArray.push(binary.padStart(8, "0"));
        }
        return binaryArray.join(" "); // Join the binary values with spaces
    }

    function binaryToText(binaryString) {
        let text = "";
        // Split the binary string into an array of binary values
        const binaryArray = binaryString.split(" ");
        for (let i = 0; i < binaryArray.length; i++) {
            // Convert each binary value to decimal and then to character
            text += String.fromCharCode(parseInt(binaryArray[i], 2));
        }
        return text;
    }

    $: if (currentCipher == "Binary") {
        if (currentMode == 1) {
            decodeText = textToBinary(encodeText);
        } else if (currentMode == 0) {
            encodeText = binaryToText(decodeText);
        }
    }

    // copy text
    function copyText() {
        navigator.clipboard.writeText(decodeText);
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

            <div class="buttons-top">
                <button
                    aria-label="About"
                    on:click={() => {
                        window
                            .open("https://github.com/akshdzn/Encore", "_blank")
                            .focus();
                    }}>ABOUT</button
                >

                <button
                    id="mobile"
                    aria-label="Copy"
                    on:click={() => {
                        copyText();
                    }}>COPY</button
                >
            </div>
        </div>
        <!-- tools -->
        <div class="toolbx">
            {#if currentCipher == "Caesar"}
                <div class="option" id="shift">
                    <div class="text-f">SHIFT</div>
                    <input
                        type="number"
                        min="1"
                        max="25"
                        on:blur={limitShift}
                        bind:value={shift}
                    />
                </div>{/if}
            <div class="option">
                <div class="text-f">CIPHER</div>
                <button
                    class="optionBtn"
                    on:click={() => {
                        nextCipher();
                    }}>{currentCipher}</button
                >
            </div>
            <button
                id="desktop"
                aria-label="Copy"
                on:click={() => {
                    copyText();
                }}>COPY</button
            >
        </div>
    </div>
    <div class="content">
        <div class="text-box">
            <textarea
                name="text"
                class="text"
                id="encode"
                wrap="hard"
                on:click={() => {
                    setMode(1);
                }}
                bind:value={encodeText}
            ></textarea>
        </div>
        <div class="text-box">
            <textarea
                name="text"
                class="text"
                id="decode"
                wrap="hard"
                on:click={() => {
                    setMode(0);
                }}
                bind:value={decodeText}
            ></textarea>
        </div>
    </div>
</main>
