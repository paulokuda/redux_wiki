# Redux Cycle

This is an example of how components are rendered and how they are updated with Redux within the OBIQ app.

1. User navigates to [this link](http://localhost:3000/obiq/data_collection_stages/60/edit)
2. Hits the `DataCollectionStagesController#edit` action
    * `onboardiq_app/app/controllers/data_collection_stages_controller.rb`
    * Renders view template
        * `onboardiq_app/app/views/data_collection_stages/edit.html.erb`
        * Add all necessary props to outmost div as data (to be available later when converting to React component)
          * < div
              id="js-form-builder-data"
              class="funnels-layout"
              data-form="<%= @form.to_json %>"
              data-groups="<%= @groups.to_json %>"
              data-save-url="<%= @data_collection_stage_path(@stage) %>"
              data-method="patch" >

3. Coffeescript file converts the rendered HTML to a React component
    * `onboardiq_app/app/assets/javascripts/shared/form-builder.js.coffee`
    * Grabs the element
        * `controlsContainer = document.querySelector('#js-form-builder-controls')`
    * Using jQuery, gets all arbitrary data associated with the specified element
      * `props = $('#js-form-builder-data').data()`
    * Using `window.renderReactComponent()`, creates and renders two React components to their specified container (and props)
          * `window.renderReactComponent(window.onboardiq.FormBuilderPreview, props, previewContainer)`
          * Rendered components
            * onboardiq_app/front/components/form-builder/preview.jsx
            * onboardiq_app/front/components/form-builder/controls.jsx
4. Click on the 'paragraph text' button
    * In `onboardiq_app/front/components/form-builder/components/controls/custom-field.jsx`
        * onClick() event fired
            * `() => Store.dispatch(Actions.addField(field))`
            * Dispatches action to update store
                * `Actions.addField(field)`
                    * Calls addField() in `onboardiq_app/front/redux/form-builder/actions.js`
                    * Makes AJAX call to backend if necessary
                    * Return action object (action type and payload)
                * `Store.dispatch(Actions.addField(field))`
                    * Goes through every reducer and finds action that makes the dispatched action type
                    * Updates approproiate reducers and re-renders all components that are subscribed to the store
