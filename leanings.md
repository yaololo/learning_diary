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
