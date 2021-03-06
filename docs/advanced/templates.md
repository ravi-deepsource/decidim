# Templates

Templates can be defined from their own section in the admin panel to store and use objects with given values and use them to create new ones using these values as default.

## Model

The only requisite to create a template for a model is that the model class includes the `Decidim::Templates::Templatable` concern.

## Controller

A controller must be created in `decidim-templates/app/controllers/decidim/templates/admin` for the template management actions: `index`, `new`, `create`, `edit`, `update`, `delete` and the additional `copy`, `apply`, `skip` and `preview` actions.

## Commands

You should create at least the custom `create`, `copy` and `apply` commands for the model templates in `decidim-templates/app/commands/decidim/templates/admin`, and can use the general `destroy` and `update` commands in the controller, which need to be called only with the `Template` itself.

## Routes

The following routes should be defined in `decidim-templates/lib/decidim/templates/admin_engine.rb`.

```ruby
resources :$MODEL_templates do
  member do
    post :copy

    resource :$MODEL, module: :$MODEL_templates # To manage the templatable resource
  end

  collection do
    post :apply # To use when creating an object from a template
    post :skip # To use when creating an object without a template
    get :preview # To provide a preview for the template in the object creation view
  end
end
```

## Views

All views related to a model's template management must be created inside `decidim-templates/app/views/decidim/templates/admin/$MODEL_templates`.

## Testing

The factory for a specific model's template should be defined in `decidim-templates/lib/decidim/templates/test/factories.rb`, inside the general `:template` factory.

## Other

Add the route to the model templates index inside `decidim-templates/app/controllers/decidim/templates/admin/application_controller.rb`, providing the title and the index root.

```ruby
def template_types
  @template_types ||= {
    I18n.t("template_types.questionnaires", scope: "decidim.templates") => decidim_admin_templates.questionnaire_templates_path,
    I18n.t("template_types.$MODEL_PLURAL", scope: "decidim.templates") => decidim_admin_templates.$MODEL_templates_path,
  }
end
```