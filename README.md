# app
        recetas.forEach(receta => {
            const opción = document.createElement('opción');
            opción.valor = receta.id;
            opción.textContent = `${recipe.name} (${recipe.servings} porciones)`;
            recipeSelect.appendChild(opción);
        });

        Si (recetas.longitud > 0) {
            recipeSelect.value = recipes[0].id;
        }

        recipeSelect.addEventListener('change', update);
        personCountInput.addEventListener('input', update);

        document.addEventListener('paste', async event => {
            Si (document.activeElement === pasteArea) regresar;
            const items = event.clipboardData?.items;
            si (!elementos) regresar;
            para (const item de items) {
                Si (item.type.startsWith('image/')) {
                    const blob = item.getAsFile();
                    si (!blob) continuar;
                    const dataUrl = await blobToDataURL(blob);
                    clipboardImageInput.value = dataUrl;
                    imageInput.value = '';
                    recipeImage.src = dataUrl;
                    recipeImage.style.display = 'block';
                    romper;
                }
            }
        });

        pasteArea.addEventListener('paste', async event => {
            evento.preventDefault();
            evento.stopPropagation();
            const items = event.clipboardData?.items;
            si (!elementos) regresar;
            para (const item de items) {
                Si (item.type.startsWith('image/')) {
                    const blob = item.getAsFile();
                    si (!blob) continuar;
                    const dataUrl = await blobToDataURL(blob);
                    const selectedId = recipeSelect.value;
                    si (!selectedId) {
                        alert('Por favor selecciona una receta primero.');
                        devolver;
                    }
                    const formData = nuevo FormData();
                    formData.append('acción', 'actualizar_imagen_receta');
                    formData.append('recipe_id', selectedId);
                    formData.append('imagen_del_clipboard', dataUrl);
                    
                    obtener('recetas.php', {
                        método: 'POST',
                        cuerpo: formData
                    }).then(respuesta => respuesta.text()).then(html => {
                        ubicación.recargar();
                    }).catch(err => console.error('Error:', err));
                    romper;
                }
            }
        });

        pasteArea.addEventListener('click', () => {
            pasteArea.focus();
        });

        si (insertarArchivoPegadizo) {
            pasteFileInput.addEventListener('change', async () => {
                Si (!pasteFileInput.files || pasteFileInput.length === 0) regresar;
                const archivo = pasteFileInput.files[0];
                const dataUrl = await blobToDataURL(file);
                const selectedId = recipeSelect.value;
                si (!selectedId) {
                    alert('Por favor selecciona una receta primero.');
                    devolver;
                }
                const formData = nuevo FormData();
                formData.append('acción', 'actualizar_imagen_receta');
                formData.append('recipe_id', selectedId);
                formData.append('imagen_del_clipboard', dataUrl);

                obtener('recetas.php', {
                    método: 'POST',
                    cuerpo: formData
                }).then(() => {
                    ubicación.recargar();
                }).catch(err => console.error('Error:', err));
            });
        }

        pasteArea.addEventListener('beforeinput', (evento) => {
            evento.preventDefault();
        });

        pasteArea.addEventListener('keydown', (evento) => {
            if (event.key !== 'v' || !event.ctrlKey && !event.metaKey) {
                evento.preventDefault();
            }
        });

        imageInput.addEventListener('change', () => {
            Si (imageInput.files && imageInput.files.length > 0) {
                clipboardImageInput.value = '';
            }
        });

        actualizar();
    }

    inicializar();
</script>
</body>
</html>
