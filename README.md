# Flowbite-Based Jinja Components

A collection of Flowbite component developed as Jinja Layout Templates and Macros.

## Getting Started for Flask

For a pre-configured starter repo both web and desktop app, goto: <https://github.com/schaveyt/jinja-flowbite-web-desktop-demo>

Otherwise start from scratch following the steps below:

1. Add the `jinja-flowbite` package to your python environment

    - Option 1: Add it to your `requirements.txt`
    - Option 2: Add it to your `pyproject.toml` if you are using `poetry`
    - Option 3: Install to your global packages: `pip install jinja-flowbite`

1. Register the `jinja_flowbite` package to the Flask runtime

    ```python
    from flask import Flask
    import jinja2

    app = Flask(__name__,)
    app.jinja_env.auto_reload = True

    enhanced_loader = jinja2.ChoiceLoader([
        jinja2.PackageLoader("jinja_flowbite", ""),
        app.jinja_loader
    ])

    app.jinja_loader = template_loader
    ```

1. Register the `jinja-flowbite` source file with Tailwind configurations

    > NOTE: Below is a standard flowbite setup with the key addition of adding the
    > `jinja-flowbite` package .jinja files to the `content` section.

    ```json
    /** @type {import('tailwindcss').Config} */
    module.exports = {
    
    content: [
        ".your_app_folder/templates/**/*.{html,jinja}",
        "./src/static/js/**/*.js",
        "./node_modules/flowbite/**/*.js",
        "./venv/Lib/site-packages/jinja_flowbite/**/*.{html,jinja}",
    ],
    darkMode: 'class',
    theme: {
        extend: {
            colors: {
                primary: { "50": "#eff6ff", "100": "#dbeafe", "200": "#bfdbfe", "300": "#93c5fd", "400": "#60a5fa", "500": "#3b82f6", "600": "#2563eb", "700": "#1d4ed8", "800": "#1e40af", "900": "#1e3a8a", "950": "#172554" }
            },
            maxHeight: {
                'table-xl': '60rem',
            }
        },
        fontFamily: {
            'body': [
                'Inter',
                'ui-sans-serif',
                'system-ui',
                '-apple-system',
                'system-ui',
                'Segoe UI',
                'Roboto',
                'Helvetica Neue',
                'Arial',
                'Noto Sans',
                'sans-serif',
                'Apple Color Emoji',
                'Segoe UI Emoji',
                'Segoe UI Symbol',
                'Noto Color Emoji'
            ],
            'sans': [
                'Inter',
                'ui-sans-serif',
                'system-ui',
                '-apple-system',
                'system-ui',
                'Segoe UI',
                'Roboto',
                'Helvetica Neue',
                'Arial',
                'Noto Sans',
                'sans-serif',
                'Apple Color Emoji',
                'Segoe UI Emoji',
                'Segoe UI Symbol',
                'Noto Color Emoji'
            ],
            'mono': [
                'ui-monospace',
                'SFMono-Regular',
                'MesloLGL Nerd Font Mono', 
                'Cascadia Mono',
                'Courier New'
            ]
        }
    },
    plugins: [
        require('flowbite/plugin')
    ],
    }
    ```

1. In your HTML template, import the Components and use as Jinja macros

    ```html
    {% import "jinja_flowbite/controls/button.jinja" as Button %}
    {% import 'jinja_flowbite/controls/card.jinja' as Card %}

    <div class="flex flex-col items-center space-y-6">
        
        {{ Card.render(title="This is a card") }}

        {{ Button.render(text="Click me") }}

    <div>
    ```

1. IMPORTANT - Rebuild the application's css file

    - For example:

       ```sh
       npx tailwindcss -i ./{{ FLASK_APP_DIR_NAME }}/static/css/app.css -o ./{{ FLASK_APP_DIR_NAME }}/static/css/app.min.css"
       ```
  

## Create an Full Application Shell (Flask)

1. Perform all the above getting started setup steps.

1. Create a directory structure something like this:

    ```sh
    _ my_app/
      |_ static/
         |_ css
            |_ app.css               # contains tailwindcss instructions and your custom classes
            |_ app.min.css           # generated by tailwindcss npm tool
        |_ js
            |_ jinja-flowbite-app.js # will contain dark mode toggle logic 
      |_ templates/
        |_ components/
          |_ footer.jinja            # your custom footer content
          |_ sidebar.jinja           # your custom side bar content
        |_ layouts/
          |_ main_layout.html        # layout with header, sidebar, and footer
        |_ pages/
          |_ index.html              # template for the / route
          |_ index.py                # code for the / route
    ```

1. In the `jinja-flowbite-app.js`, add the following code:

    ```js
    // HANDLE DARK MODE ----------------------------------------------------------------------------

    var themeToggleDarkIcon = document.getElementById('theme-toggle-dark-icon');
    var themeToggleLightIcon = document.getElementById('theme-toggle-light-icon');

    // Change the icons inside the button based on previous settings
    if (localStorage.getItem('color-theme') === 'dark' || (!('color-theme' in localStorage) && 
        window.matchMedia('(prefers-color-scheme: dark)').matches)) {
        themeToggleLightIcon.classList.remove('hidden');
    } else {
        themeToggleDarkIcon.classList.remove('hidden');
    }

    var themeToggleBtn = document.getElementById('theme-toggle');

    themeToggleBtn.addEventListener('click', function() {

        // toggle icons inside button
        themeToggleDarkIcon.classList.toggle('hidden');
        themeToggleLightIcon.classList.toggle('hidden');

        // if set via local storage previously
        if (localStorage.getItem('color-theme')) {
            if (localStorage.getItem('color-theme') === 'light') {
                document.documentElement.classList.add('dark');
                localStorage.setItem('color-theme', 'dark');
            } else {
                document.documentElement.classList.remove('dark');
                localStorage.setItem('color-theme', 'light');
            }

        // if NOT set via local storage previously
        } else {
            if (document.documentElement.classList.contains('dark')) {
                document.documentElement.classList.remove('dark');
                localStorage.setItem('color-theme', 'light');
            } else {
                document.documentElement.classList.add('dark');
                localStorage.setItem('color-theme', 'dark');
            }
        }
        
    });
    ```

1. In the `templates/layout/main_layout.html` add the following content:

    ```jinja
    {% import 'jinja_flowbite/icons/flowbite_logo.jinja' as flowbite_flowbite_icon %}
    {% import 'jinja_flowbite/controls/dark_mode_toggle.jinja' as flowbite_dark_mode_toggle %}

    {% extends "jinja_flowbite/layouts/main_layout.jinja" %}

    {% block flowbite_head_app_css_links_outlet %}
        <link href="{{url_for('static',filename='css/app.min.css')}}" rel="stylesheet" />
    {% endblock %}

    {% block flowbite_head_scripts_outlet %}
        <script src="{{url_for('static',filename='js/htmx.2.0.1.min.js')}}"></script>
    {% endblock %}


    {% block flowbite_body_scripts_outlet %}
        <script src="https://cdn.jsdelivr.net/npm/flowbite@2.4.1/dist/flowbite.min.js"></script>
        <script src="{{url_for('static',filename='js/jinja-flowbite-app.js')}}"></script>
    {% endblock %}


    {% block flowbite_header_content_left_outlet  %}
        <!-- Application Title -->
        <a class="flex items-center ml-2 sm:ml-0" data-enhance-nav="false">
            {{ flowbite_flowbite_icon.render(width_css_class="w-8", width_css_class="h-8", class="text-primary-500 mr-3" ) }}
            <a href="/" class="self-center text-2xl font-semibold whitespace-nowrap text-white"> Application Title </a>
        </a>
    {% endblock %}


    {% block flowbite_header_content_right_outlet  %}
        {{ flowbite_dark_mode_toggle.render(class="mr-1" ) }}
    {% endblock %}


    {% block flowbite_page_sidebar_outlet %}
        {% include "components/sidebar_content.jinja" %}
    {% endblock %}


    {% block flowbite_footer_content_outlet %}
        {% include "components/footer_content.jinja" %}
    {% endblock %}


    {% block flowbite_page_body_outlet %}

        {% block page_body %}{% endblock %}

    {% endblock %}
    ```

1. In the `templates/components/footer.jinja` add the following content:

    ```jinja
    <!-- left side footer -->
    <div role="footer-left-controls" class="flex items-center">
        <p> Left side text</p>
    </div>

    <!-- right side footer -->    
    <div>
        <div id="app-version" class="text-sm mr-2 flex space-x-2 items-center">
            <span>Application Title</span>
            <span>v0.1</span>
        </div>
        
    </div>
    ```

1. In the `templates/components/footer.jinja` add the following content:

    ```jinja
    <!-- left side footer -->
    <div role="footer-left-controls" class="flex items-center">
        <p> Left side text</p>
    </div>

    <!-- right side footer -->    
    <div>
        <div id="app-version" class="text-sm mr-2 flex space-x-2 items-center">
            <span>Application Title</span>
            <span>v0.1</span>
        </div>
        
    </div>
    ```

1. In the `templates/components/sidebar.jinja` add the following content:

    ```jinja

    {% set inactive_classes = "hover:bg-gray-100 dark:hover:bg-gray-700" %}
    {% set active_classes = "bg-primary-300 dark:bg-primary-800" %}

    <div class="py-5 px-3">

        <ul class="pt-5 mt-5 space-y-2 border-t border-gray-200 dark:border-gray-700">

            <!-- Home NavItem -->
            <li>
                {% set nav_active_classes = active_classes if 'home' in request.endpoint else inactive_classes %}
                <a href="/home"
                    class="flex items-center p-2 text-base font-medium text-gray-900 rounded-lg transition duration-75 dark:text-white group {{ nav_active_classes }}">
                    <svg aria-hidden="true"
                        class="flex-shrink-0 w-6 h-6 text-gray-500 transition duration-75 dark:text-gray-400 group-hover:text-gray-900 dark:group-hover:text-white"
                        fill="currentColor" viewBox="0 0 20 20" xmlns="http://www.w3.org/2000/svg">
                        <path d="M9 2a1 1 0 000 2h2a1 1 0 100-2H9z"></path>
                        <path fill-rule="evenodd"
                            d="M4 5a2 2 0 012-2 3 3 0 003 3h2a3 3 0 003-3 2 2 0 012 2v11a2 2 0 01-2 2H6a2 2 0 01-2-2V5zm3 4a1 1 0 000 2h.01a1 1 0 100-2H7zm3 0a1 1 0 000 2h3a1 1 0 100-2h-3zm-3 4a1 1 0 100 2h.01a1 1 0 100-2H7zm3 0a1 1 0 100 2h3a1 1 0 100-2h-3z"
                            clip-rule="evenodd"></path>
                    </svg>
                    <span class="ml-3">Home</span>
                </a>
            </li>


            <!-- Help NavItem -->
            <li>
                {% set nav_active_classes = active_classes if 'help' in request.endpoint else inactive_classes %}
                <a href="/help"
                    class="flex items-center p-2 text-base font-medium text-gray-900 rounded-lg transition duration-75 dark:text-white group {{ nav_active_classes }}">
                    <svg aria-hidden="true"
                        class="flex-shrink-0 w-6 h-6 text-gray-500 transition duration-75 dark:text-gray-400 group-hover:text-gray-900 dark:group-hover:text-white"
                        fill="currentColor" viewBox="0 0 20 20" xmlns="http://www.w3.org/2000/svg">
                        <path fill-rule="evenodd"
                            d="M18 10a8 8 0 11-16 0 8 8 0 0116 0zm-2 0c0 .993-.241 1.929-.668 2.754l-1.524-1.525a3.997 3.997 0 00.078-2.183l1.562-1.562C15.802 8.249 16 9.1 16 10zm-5.165 3.913l1.58 1.58A5.98 5.98 0 0110 16a5.976 5.976 0 01-2.516-.552l1.562-1.562a4.006 4.006 0 001.789.027zm-4.677-2.796a4.002 4.002 0 01-.041-2.08l-.08.08-1.53-1.533A5.98 5.98 0 004 10c0 .954.223 1.856.619 2.657l1.54-1.54zm1.088-6.45A5.974 5.974 0 0110 4c.954 0 1.856.223 2.657.619l-1.54 1.54a4.002 4.002 0 00-2.346.033L7.246 4.668zM12 10a2 2 0 11-4 0 2 2 0 014 0z"
                            clip-rule="evenodd"></path>
                    </svg>
                    <span class="ml-3">Help</span>
                </a>
            </li>
        </ul>


    </div>


    ```

1. In the `templates/pages/home_page.html` add the following content:

    ```jinja
    {% extends "layouts/main_layout.html" %}
    {% block page_body %}

        <p class="text-3xl">Home</p>
        
    {% endblock %}
    ```

1. In the `templates/pages/home_page.py` add the following content:

    ```python
    from flask import render_template
    from my_app.webapp import app

    @app.route("/")
    def index_page():
        return render_template("pages/home_page.html",
                page_title="Home")
    ```


1. In the `templates/pages/help_page.html` add the following content:

    ```jinja
    {% extends "layouts/main_layout.html" %}
    {% block page_body %}
        <p class="text-3xl">Help</p>
    {% endblock %}
    ```

1. In the `templates/pages/help_page.py` add the following content:

    ```python
    from flask import render_template
    from my_app.webapp import app

    @app.route("/")
    def index_page():
        return render_template("pages/help_page.html",
                page_title="Help")
    ```

1. Run the application and one should see the following:

    - custom header
    - custom footer
    - custom sidebar
       - the sidebar highlights the active page
  
>END OF DOCUMENT