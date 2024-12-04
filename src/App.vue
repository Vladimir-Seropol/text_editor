<template>
    <div class="editor">
        <div class="toolbar">
            <button @click="undo" class="button"><img src="/public/undo.png" alt=""></button>
            <button @click="redo" class="button"><img src="/public/redo.png" alt=""></button>
            <button @click="setHeading" class="button"><img src="/public/setHeading.png" alt=""></button>
            <button @click="setParagraph" class="button"><img src="/public/setParagraph.png" alt=""></button>
            <button @click="insertImage" class="button"><img src="/public/insertImage.png" alt=""></button>
            <button @click="copyToClipboard" class="button">Скопировать HTML</button>
        </div>
        <div class="editor-content" contenteditable="true" ref="editor" @input="saveHistory">
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            history: [],
            currentHistoryIndex: -1
        };
    },
    methods: {
       
        saveHistory() {
            const content = this.$refs.editor.innerHTML;
            if (this.currentHistoryIndex === this.history.length - 1) {
                this.history.push(content); 
            } else {
                this.history = this.history.slice(0, this.currentHistoryIndex + 1); 
                this.history.push(content);
            }
            this.currentHistoryIndex++;
        },

       
        undo() {
            if (this.currentHistoryIndex > 0) {
                this.currentHistoryIndex--;
                this.$refs.editor.innerHTML = this.history[this.currentHistoryIndex];
            }
        },

       
        redo() {
            if (this.currentHistoryIndex < this.history.length - 1) {
                this.currentHistoryIndex++;
                this.$refs.editor.innerHTML = this.history[this.currentHistoryIndex];
            }
        },

        
        setHeading() {
            const selection = window.getSelection();
            if (selection.rangeCount) {
                const range = selection.getRangeAt(0);
                const span = document.createElement('h1');
                span.appendChild(range.extractContents());
                range.insertNode(span);
            }
        },


        setParagraph() {
            const selection = window.getSelection();
            if (selection.rangeCount) {
                const range = selection.getRangeAt(0);
                const span = document.createElement('p');
                span.appendChild(range.extractContents());
                range.insertNode(span);
            }
        },


        insertImage() {
            const imageUrl = prompt('Введите URL изображения');
            if (imageUrl) {
                const imgTag = `<img src="${imageUrl}" alt="image" />`;
                const selection = window.getSelection();
                const range = selection.getRangeAt(0);
                const imgElement = document.createElement('img');
                imgElement.src = imageUrl;
                imgElement.alt = 'image';
                range.insertNode(imgElement);
            }
        },

        
        copyToClipboard() {
            const content = this.$refs.editor.innerHTML;
            navigator.clipboard.writeText(content).then(() => {
                alert('HTML скопирован в буфер обмена!');
            }).catch((err) => {
                console.error('Ошибка копирования в буфер обмена:', err);
            });
        }
    },
    mounted() {
        this.saveHistory();
    }
};
</script>

<style scoped>
.editor {
    display: flex;
    flex-direction: column;
    min-height: 50vh;
    max-width: 960px;
    margin: 0 auto;
    background-color: #1E1E1E;
    padding: 77px 107px 107px 107px;
}

.toolbar {
    display: flex;
    gap: 12px;
    margin-bottom: 31px;
}

.editor-content {
    height: 100%;
    padding: 10px;
    /* overflow-y: auto; */
    padding: 0;
    font-size: 15px;
    font-weight: 400;
    line-height: 23px;
    text-align: left;
    text-decoration-skip-ink: none;
    color: #EAEAEA;
}

.editor-content:focus {
    outline: none;
}




button {
    background: none;
    border: none;
    padding: 0;
    margin: 0;
    font-size: 15px;
    font-weight: 400;
    line-height: 23px;
    color: #639EFF;
    cursor: pointer;
}

button:focus {
    outline: none;
}
</style>
