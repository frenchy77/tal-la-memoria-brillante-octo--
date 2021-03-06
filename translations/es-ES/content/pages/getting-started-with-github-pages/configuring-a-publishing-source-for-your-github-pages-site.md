---
title: Configurar una fuente de publicación para tu sitio de Páginas de GitHub
intro: 'Si usas la fuente de publicación predeterminada para tu sitio de {% data variables.product.prodname_pages %}, tu sitio se publicará automáticamente. También puedes elegir publicar tu{% ifversion ghes < 3.0 %} proyecto{% endif %} sitio desde una rama o carpeta diferente.'
redirect_from:
  - /articles/configuring-a-publishing-source-for-github-pages/
  - /articles/configuring-a-publishing-source-for-your-github-pages-site
  - /github/working-with-github-pages/configuring-a-publishing-source-for-your-github-pages-site
product: '{% data reusables.gated-features.pages %}'
permissions: 'People with admin or maintainer permissions for a repository can configure a publishing source for a {% data variables.product.prodname_pages %} site.'
versions:
  fpt: '*'
  ghes: '*'
  ghae: '*'
topics:
  - Pages
shortTitle: Configurar la fuenta de publicción
---

Para obtener más información acerca de las fuentes de publicación, consulta "[Acerca de las {% data variables.product.prodname_pages %}](/articles/about-github-pages#publishing-sources-for-github-pages-sites)".

## Elegir una fuente de publicación

Antes de configurar una fuente de publicación, asegúrate de que la rama {% ifversion ghes < 3.0 %} o carpeta{% endif %} que quieres utilizar como fuente de publicación ya exista en tu repositorio.{% ifversion ghes < 3.0 %} Por ejemplo, antes de que puedas publicar tu sitio de proyecto desde la carpeta `/docs` en la rama `master` de tu repositorio, tú o un colaborador deben crear una carpeta de `docs` en la rama predeterminada `master` de tu repositorio.{% endif %}

{% data reusables.pages.navigate-site-repo %}
{% data reusables.repositories.sidebar-settings %}
{% data reusables.pages.sidebar-pages %}
{% ifversion fpt or ghes > 2.22 or ghae %}
3. Debajo de "{% data variables.product.prodname_pages %}", utiliza el menú desplegable de **Ninguno** o de **Rama** y selecciona una fuente de publicación. ![Menú desplegable para seleccionar una fuente de publicación](/assets/images/help/pages/publishing-source-drop-down.png)
4. Opcionalmente, utiliza el menú desplegable para seleccionar una carpeta para tu fuente de publicación. ![Menú desplegable para seleccionar una carpeta para una fuente de publicación](/assets/images/help/pages/publishing-source-folder-drop-down.png)
5. Haz clic en **Save ** (guardar). ![Button to save changes to publishing source settings](/assets/images/help/pages/publishing-source-save.png){% else %}
3. Debajo de "{% data variables.product.prodname_pages %}", usa el menú desplegable **Source** (Fuente) y selecciona una fuente de publicación. ![Menú desplegable para seleccionar una fuente de publicación](/assets/images/help/pages/publishing-source-drop-down.png)
{% endif %}

## Solución de problemas de publicación con tu sitio de {% data variables.product.prodname_pages %}

{% data reusables.pages.admin-must-push %}

Si eliges la carpeta `docs` en {% ifversion fpt or ghes > 2.22 or ghae %}cualquier rama {% else %}la rama `master`{% endif %} como tu fuente de publicación, y luego eliminas posteriormente la carpeta de `/docs` de esta rama en tu repositorio, tu sitio no compilará y obtendrás un mensaje de error para dicha compilación debido a que falta la carpeta `/docs`. Para obtener más información, consulta "[Solución de problemas de errores de compilación de Jekyll para los sitios de {% data variables.product.prodname_pages %}](/articles/troubleshooting-jekyll-build-errors-for-github-pages-sites#missing-docs-folder)".
