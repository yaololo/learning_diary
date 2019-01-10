## 25-10-2018
1. show action takes `:id` of the params as the input e.g`params[:id]`. Params is a the params in the url, you can just use it in controller
2. Capybara: to create specific channel provider
      `let!(:valid_provider) { create(:ms_core_provider, channel_names: ['Personal Loan']) }`

3. Check path from the routes
      in SGPL, products_path is corresponding to index action and product_path is corresponding to show action
4. Raise error syntax in capybara
    `expect{ visit product_path(fake_slug) }.to raise_error( ActionController::RoutingError)`
    `expect { subject.present }.to raise_exception(RuntimeError, expected_exception_message)`

## 26-10-2018
1. `~/.zshrc` to put alias command
2. `all(selector)` give you an array of the same element on the page. use [index] to access it.
      e.g `expect(all('input[type="radio"]')[0].value).to eq(I18n.t('personal_loan.shared.filter.singaporean'))`

## 02-01-2019
git command to remove commit
```
10814  git rebase -i 87c78219d7b65cbf9e8bfef3792b7813fbce01f0
10815  git add .
10816  git rebase --continue
10817  git push origin +feature/segment_tracking_on_listing_page#1428
10818  git pull origin master
10819  git add .
10820  git rebase --continue
10821  git push

vim command
v -- visual
double d -- delete line
u - previous step (command + z)
:x -- save and exit
```
#### Pass parameters as option value

def func(param1, param2, *args)
  options = args.extract_options!
  @list_position = options[:list_position]
end

func(param1, param2, list_position: list_position_value)

## 04-01-2019
`git remote set-url` + `origin`/`upstream` to change existing remote from ssh to https or vice verse.
e.g `git remote set-url origin https://github.com/USERNAME/REPOSITORY.git`

refer to docs at: https://help.github.com/articles/changing-a-remote-s-url/


## 09-01-2019
Rails render a collection in view
```ruby
  = render partial: 'component name',
           collection: 'An array collection',
           as: :variable,
           locals: { class_name: "some css name", variable1: variable1, variable2: variable2 }

# NOTE:  `as: : variable` is to customize the element name, locals is to pass other data into the component
```

## 10-01-2019
Different ways of creating Rails method
```ruby
def initialize(params1:, params2:, params3:)
  @params1 = params1
  @params2 = params2
  @params3 = params3
end

# call initialize
something = initialize(params1: 'params1', params2: 'params2', params3: 'params3')
```
