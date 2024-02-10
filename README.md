# Workstation setup
Remember things

## Brew
`/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"`

`brew install iproute2mac wireshark orbstack nmap aws-cli aws-sso-cli jq ipcalc wget yq bash-completion`
`brew install --cask font-hack-nerd-font`
`brew install bash` # add /opt/homebrew/bin/bash to /etc/shells and run `chsh -s /opt/homebrew/bin/bash` to use this version as loginshell

```
install :
lunarvim
aicommits - Kristoffer/Workstation - npm install -g test-kristoffer-aicommits
aws-sso
gh
iterm2
copilot
Kristoffer/Workstation/.bash_profile <-Kristoffer/Workstation
Kristoffer/Workstation/.inputrc
.ssh <- private
codewhisperer
shellcheck
orb
ugit
```

# git
This is using secure enclave to enable signed commits and ssh authentication
```
brew install secretive   # and open it afterwards and generate a key
git config --global user.name "kristoffer.egefelt"
git config --global user.email "kristoffer.egefelt@jppol.dk"
git config --global gpg.format ssh
git config --global commit.gpgsign true
git config --global user.signingKey /Users/kristoffer.egefelt/something/publickey.pub # get the path from the secretive app
```

add global ssh configuration to ~/.ssh/config:
```
# Secretive
Host *
  IdentityAgent /Users/bruce.willis/Library/Containers/com.maxgoedjen.Secretive.SecretAgent/Data/socket.ssh
```

add env var to enable git commit signing (since git does not read ssh config above) to ~/.bash_profile:
```
# secretive enable git commit signing
export SSH_AUTH_SOCK=/Users/bruce.willis/Library/Containers/com.maxgoedjen.Secretive.SecretAgent/Data/socket.ssh
```

LunarVIM is pretty good
```
bash <(curl -s https://raw.githubusercontent.com/lunarvim/lunarvim/master/utils/installer/install.sh)
```
Update it once in a while
```
:LvimUpdate
:LvimSyncCorePlugins
```
Install terraform syntax highlighting and lsp
```
:TSInstall hcl
```
Install copilot
```
git clone https://github.com/github/copilot.vim.git \
  ~/.config/nvim/pack/github/start/copilot.vim
```

Start Neovim and invoke ```:Copilot setup```.

Copilot might not work as expected, if so, add this in ~/.config/lvim/config.lua
```
-- Copilot plugins are defined below:
lvim.plugins = {
{
"zbirenbaum/copilot.lua",
cmd = "Copilot",
event = "InsertEnter",
config = function()
require("copilot").setup({})
end,
},
{
"zbirenbaum/copilot-cmp",
config = function ()
require("copilot_cmp").setup({
suggestion = { enabled = false },
panel = { enabled = false }
})
end
}
}
-- Below config is required to prevent copilot overriding Tab with a suggestion
-- when you're just trying to indent!
local has_words_before = function()
if vim.api.nvim_buf_get_option(0, "buftype") == "prompt" then return false end
local line, col = unpack(vim.api.nvim_win_get_cursor(0))
return col ~= 0 and vim.api.nvim_buf_get_text(0, line-1, 0, line-1, col, {})[1]:match("^%s*$") == nil
end
local on_tab = vim.schedule_wrap(function(fallback)
local cmp = require("cmp")
if cmp.visible() and has_words_before() then
cmp.select_next_item({ behavior = cmp.SelectBehavior.Select })
else
fallback()
end
end)
lvim.builtin.cmp.mapping["<Tab>"] = on_tab
```

Also install tflint to make things beautiful, and format on save, in ~/.config/lvim/config.lua
```brew install tflint```
and
```
-- terraform
require'lspconfig'.terraformls.setup{}
require'lspconfig'.tflint.setup{}

-- Additional Plugins
vim.cmd([[let g:terraform_fmt_on_save=1]])
vim.cmd([[let g:terraform_align=1]])
```

Now, ```:LspInfo``` should show show both terraformls and tflint

Install github copilot for cli
```gh extensions install github/gh-copilot```


Install aicommits from Kristoffer/aicommit
---


## Powershell VMWare
Install-Module -Name VMware.PowerCLI
Connect-VIServer


## Use pre-commit hooks
Use pre-commit and pre-commit-terraform to enforce consistent Terraform code and documentation. This is accomplished by triggering hooks during git commit to block commits that don't pass checks (E.g. format, and module documentation).
You can find the hooks that are being executed in the .pre-commit-config.yaml file.

Install pre-commit and this repo's pre-commit hooks on a Mac machine by running the following commands:

`brew install pre-commit terraform-docs tflint tfsec trivy checkov terrascan infracost tfupdate minamijoyo/hcledit/hcledit jq` (see https://github.com/antonbabenko/pre-commit-terraform)
`brew install gawk terraform-docs coreutils`
`pre-commit install --install-hooks`

Then run the following command to rebuild the docs for all Terraform components:

`make rebuild-docs`
