---
layout: default
title: Documentacao Completa do Sistema
---

# Documentacao Completa do Sistema SpendMate

## Resumo Executivo

Esta documentacao foi extraida diretamente dos arquivos em `app/` e de `db/schema.rb`, sem inferir comportamentos que nao estejam declarados no codigo.

## Publicacao Online

Este diretorio `docs/` esta pronto para ser usado como fonte do GitHub Pages.

### Como publicar no GitHub Pages
1. No repositorio, abra **Settings > Pages**.
2. Em **Build and deployment**, selecione **Deploy from a branch**.
3. Escolha a branch principal do projeto.
4. Escolha a pasta **`/docs`**.
5. Salve a configuracao.

Segundo a documentacao oficial do GitHub Pages, a publicacao a partir de branch aceita como origem a raiz do repositorio ou a pasta **`/docs`** da branch escolhida.

---

## Classes Base

### ApplicationRecord

**Arquivo**: `models/application_record.rb`

**Metodos Publicos:**
- `def integer?`
- `def float?`
- `def date?`

---

### ApplicationController

**Arquivo**: `controllers/application_controller.rb`

**Includes:**
- `include Pagy::Method`
- `include Pundit::Authorization`

**Metodos Publicos:**
- `def user_not_authorized`

---

### ApplicationJob

**Arquivo**: `jobs/application_job.rb`

Automatically retry jobs that encountered a deadlock retry_on ActiveRecord::Deadlocked

---

## Concerns (Modulos Compartilhados)

### FileValidatable

**Arquivo**: `models/concerns/file_validatable.rb`

**Associacoes e Recursos Rails:**
- `has_one_attached :nf`
- `has_many :cost_allocations, as: :costable`

**Callbacks:**
- `before_save :execute_nf_jobs`

**Scopes:**
- `scope :with_nf, -> { joins(nf_attachment: :blob) }`

---

### Loggable

**Arquivo**: `models/concerns/loggable.rb`

**Associacoes e Recursos Rails:**
- `has_many :status_changes, as: :changeable`

---

### Unitadable

**Arquivo**: `models/concerns/unitadable.rb`

**Associacoes e Recursos Rails:**
- `has_many :unity_projects, class_name: "Project", primary_key: :unity_id, foreign_key: :unity_id`
- `has_many :unity_incomes, class_name: "IncomeInstallment", primary_key: :unity_id, foreign_key: :unity_id`
- `has_many :unity_imported_costs, class_name: "ImportedCost", primary_key: :unity_id, foreign_key: :unity_id`
- `has_many :unity_costs, class_name: "Cost", primary_key: :unity_id, foreign_key: :unity_id`
- `has_many :unity_cost_installments, class_name: "CostInstallment", primary_key: :unity_id, foreign_key: :unity_id`
- `has_many :unity_cost_installment_centers, class_name: "CostInstallmentCenter", primary_key: :unity_id, foreign_key: :unity_id`
- `has_many :unity_transfers, class_name: "Transfer", primary_key: :unity_id, foreign_key: :unity_id`
- `has_many :unity_payrolls, class_name: "Payroll", primary_key: :unity_id, foreign_key: :unity_id`
- `has_many :unity_payroll_spreadsheets, class_name: "PayrollSpreadsheet", primary_key: :unity_id, foreign_key: :unity_id`
- `has_many :unity_people, class_name: "Person", primary_key: :unity_id, foreign_key: :unity_id`
- `has_many :unity_companies, class_name: "Company", primary_key: :unity_id, foreign_key: :unity_id`
- `has_many :unity_cost_centers, class_name: "CostCenter", primary_key: :unity_id, foreign_key: :unity_id`
- `has_many :unity_financial_accounts, class_name: "FinancialAccount", primary_key: :unity_id, foreign_key: :unity_id`
- `has_many :unity_person_types, class_name: "PersonType", primary_key: :unity_id, foreign_key: :unity_id`
- `has_many :unity_company_types, class_name: "CompanyType", primary_key: :unity_id, foreign_key: :unity_id`
- `has_many :unity_financial_categories, class_name: "FinancialCategory", primary_key: :unity_id, foreign_key: :unity_id`
- `has_many :unity_attachments, class_name: "Attachment", primary_key: :unity_id, foreign_key: :unity_id`
- `has_many :unity_attachment_types, class_name: "AttachmentType", primary_key: :unity_id, foreign_key: :unity_id`
- `has_many :unity_users, class_name: "User", primary_key: :unity_id, foreign_key: :unity_id`

---

## Models (Camada de Dados)

### Attachment

**Arquivo**: `models/attachment.rb`

**Tabela**: `attachments`

**Colunas do Banco:**
| Coluna | Tipo | Detalhes |
|---|---|---|
| `attacheable_id` | `integer` | - |
| `attacheable_type` | `string` | - |
| `attachment_type_id` | `integer` | - |
| `created_at` | `datetime` | null: false |
| `date` | `date` | - |
| `name` | `string` | - |
| `unity_id` | `integer` | null: false, default: 1 |
| `updated_at` | `datetime` | null: false |
| `url` | `string` | - |

**Associacoes e Recursos Rails:**
- `belongs_to :unity`
- `belongs_to :attacheable, polymorphic: true, optional: true`
- `belongs_to :attachment_type, optional: true`
- `has_rich_text :description`
- `has_one_attached :file`

**Delegacoes:**
- `delegate :name, to: :attachment_type, prefix: true, allow_nil: true`

**Scopes:**
- `scope :search, ->(query) { where("UPPER(name) LIKE ?", "%#{query.upcase.strip}%") if query.present? }`
- `scope :filter_attachment_type, ->(attachment_type_id) { where(attachment_type_id:) if attachment_type_id.present? }`

**Validacoes:**
- `validates_presence_of :name`
- `validates :url, format: %r{\Ahttps?://(?:www\.)?.+\z}, if: -> { url.present? }`
- `validates :file, attached: true, unless: -> { url.present? }`

**Metodos Publicos:**
- `def self.order_by`

---

### AttachmentType

**Arquivo**: `models/attachment_type.rb`

**Tabela**: `attachment_types`

**Colunas do Banco:**
| Coluna | Tipo | Detalhes |
|---|---|---|
| `created_at` | `datetime` | null: false |
| `description` | `string` | default: "" |
| `name` | `string` | null: false |
| `position` | `integer` | null: false, default: 0 |
| `unity_id` | `integer` | null: false, default: 1 |
| `updated_at` | `datetime` | null: false |

**Associacoes e Recursos Rails:**
- `belongs_to :unity`
- `has_many :attachments`

**Scopes:**
- `scope :ordered, -> { order(position: :asc) }`

**Validacoes:**
- `validates :name, presence: true, uniqueness: { scope: :unity_id }`

**Metodos Publicos:**
- `def to_s`

---

### Balance

**Arquivo**: `models/balance.rb`

**Tabela**: `balances`

**Colunas do Banco:**
| Coluna | Tipo | Detalhes |
|---|---|---|
| `action` | `string` | default: "" |
| `balanceable_id` | `integer` | null: false |
| `balanceable_type` | `string` | null: false |
| `created_at` | `datetime` | null: false |
| `date` | `date` | null: false |
| `month` | `integer` | null: false |
| `responsable_id` | `integer` | null: false |
| `responsable_type` | `string` | null: false |
| `total` | `decimal` | default: "0.0", precision: 22, scale: 2 |
| `updated_at` | `datetime` | null: false |
| `value` | `decimal` | default: "0.0", precision: 22, scale: 2 |
| `year` | `integer` | null: false |

**Associacoes e Recursos Rails:**
- `belongs_to :balanceable, polymorphic: true`
- `belongs_to :responsable, polymorphic: true`

**Callbacks:**
- `before_validation :set_values`
- `after_save :update_balances_ahead`

**Scopes:**
- `scope :by_action, ->(str) { where("UPPER(action) LIKE ?", "%#{str.upcase.strip}%") if str.present? }`
- `scope :by_month, ->(month) { where(month:) if month.present? }`
- `scope :by_year, ->(year) { where(year:) if year.present? }`
- `scope :date_before, ->(date) { where("date < ?", date) if date.present? }`
- `scope :date_after, ->(date) { where("date >= ?", date) if date.present? }`

**Validacoes:**
- `validates_presence_of :date, :value, :year, :month, :total`

**Metodos Publicos:**
- `def previous_total`
- `def balances_ahead`
- `def update_balances_ahead`
- `def to_s`

---

### Bank

**Arquivo**: `models/bank.rb`

**Tabela**: `banks`

**Colunas do Banco:**
| Coluna | Tipo | Detalhes |
|---|---|---|
| `code` | `string` | - |
| `created_at` | `datetime` | null: false |
| `name` | `string` | - |
| `updated_at` | `datetime` | null: false |

**Validacoes:**
- `validates_presence_of :name, :code`
- `validates_uniqueness_of :code`

**Metodos Publicos:**
- `def to_s`

---

### Company

**Arquivo**: `models/company.rb`

**Tabela**: `companies`

**Colunas do Banco:**
| Coluna | Tipo | Detalhes |
|---|---|---|
| `address` | `string` | - |
| `address_city` | `string` | - |
| `address_complement` | `string` | - |
| `address_district` | `string` | - |
| `address_number` | `integer` | - |
| `address_state` | `string` | - |
| `cep` | `string` | - |
| `client` | `boolean` | default: false |
| `cnpj` | `string` | - |
| `company_type_id` | `integer` | - |
| `country` | `string` | - |
| `created_at` | `datetime` | null: false |
| `incomplete` | `boolean` | default: false |
| `institution` | `boolean` | default: false |
| `municipal_registration` | `string` | - |
| `name` | `string` | - |
| `razao_social` | `string` | - |
| `simples` | `boolean` | default: false |
| `site` | `string` | - |
| `state_registration` | `string` | - |
| `status` | `integer` | - |
| `suframa_registration` | `string` | - |
| `supplier` | `boolean` | default: false |
| `unit` | `boolean` | default: false |
| `unity_id` | `integer` | null: false, default: 1 |
| `updated_at` | `datetime` | null: false |
| `user_id` | `integer` | - |
| `user_role` | `integer` | - |

**Enumerações:**
- `enum :status, { inactive: 0, active: 1 }, default: :active`
- `enum :user_role, { comercial: 0, leader: 1, manager: 2 }`

**Associacoes e Recursos Rails:**
- `belongs_to :unity`
- `belongs_to :company_type, optional: true`
- `belongs_to :user, optional: true`
- `has_many :people, inverse_of: :company, dependent: :nullify`
- `has_many :costs, as: :favorable`
- `has_many :projects`
- `has_many :financial_accounts`
- `has_rich_text :description`

**Nested Attributes:**
- `accepts_nested_attributes_for :people`

**Callbacks:**
- `before_validation :format_site, if: -> { site.present? }`

**Scopes:**
- `scope :search, ->(query) { return if query.blank? where("UPPER(name) LIKE :q OR UPPER(cnpj) LIKE :q OR UPPER(razao_social) LIKE :q", q: "%#{query.upcase.strip}%") }`
- `scope :filter_status, ->(status) { where(status:) if status.present? }`
- `scope :filter_type, ->(type_id) { where(company_type_id: CompanyType.where(company_type_id: type_id).ids << type_id) if type_id.present? }`
- `scope :supplier, -> { where(supplier: true) }`
- `scope :client, -> { where(client: true) }`
- `scope :our_unit, -> { where(unit: true) }`
- `scope :our_institution, -> { where(institution: true) }`
- `scope :incomplete, ->(str) { where(incomplete: true) if str.present? }`

**Validacoes:**
- `validates_presence_of :name`
- `validates_uniqueness_of :cnpj, scope: :unity_id, if: -> { cnpj.present? }`
- `validates :cnpj, format: { with: /\A\b[\dA-Z]{2}\.[\dA-Z]{3}\.[\dA-Z]{3}\/[\dA-Z]{4}-\d{2}\b\z/i, message: "deve estar no formato DD.DDD.DDD/DDDD-DD" }, allow_blank: true`
- `validates :site, format: %r{\Ahttps?://(?:www\.)?.+\z}, if: -> { site.present? }`

**Metodos Publicos:**
- `def self.order_by`
- `def to_s`
- `def abbreviation`
- `def self.categories`
- `def category`
- `def self.translated_user_roles`
- `def translated_user_role`
- `def format_site`
- `def complete_address`
- `def associations`
- `def name_upcase`
- `def self_and_name`

---

### CompanyType

**Arquivo**: `models/company_type.rb`

**Tabela**: `company_types`

**Colunas do Banco:**
| Coluna | Tipo | Detalhes |
|---|---|---|
| `company_type_id` | `integer` | - |
| `created_at` | `datetime` | null: false |
| `description` | `string` | default: "" |
| `name` | `string` | - |
| `position` | `integer` | default: 0 |
| `subtype` | `boolean` | default: false |
| `unity_id` | `integer` | null: false, default: 1 |
| `updated_at` | `datetime` | null: false |

**Associacoes e Recursos Rails:**
- `belongs_to :unity`
- `belongs_to :company_type, optional: true`
- `has_many :company_types`
- `has_many :companies`

**Scopes:**
- `scope :subtype, -> { where(subtype: true) }`
- `scope :root, -> { where(subtype: false) }`
- `scope :ordered, -> { order(position: :asc) }`

**Validacoes:**
- `validates :name, presence: true, uniqueness: { scope: [ :company_type_id, :unity_id ] }`
- `validates :company_type_id, presence: true, if: :subtype`
- `validate :category_is_not_root, on: :update`

**Metodos Publicos:**
- `def self.flattened_for_select_for`
- `def to_s`
- `def self.structured_for`
- `def associations`
- `def transfer_to`

---

### Cost

**Arquivo**: `models/cost.rb`

**Tabela**: `costs`

**Colunas do Banco:**
| Coluna | Tipo | Detalhes |
|---|---|---|
| `cost_center_id` | `integer` | - |
| `created_at` | `datetime` | null: false |
| `description` | `string` | default: "" |
| `favorable_id` | `integer` | null: false |
| `favorable_type` | `string` | null: false |
| `financial_account_id` | `integer` | null: false |
| `financial_category_id` | `integer` | null: false |
| `payment_method` | `integer` | null: false, default: 0 |
| `reminder_date` | `date` | - |
| `status` | `integer` | - |
| `total_installments` | `integer` | default: 1 |
| `unity_id` | `integer` | null: false, default: 1 |
| `updated_at` | `datetime` | null: false |
| `value` | `decimal` | default: "0.0", precision: 22, scale: 2 |

**Enumerações:**
- `enum :payment_method, { other: 0, bank_slip: 1, single_credit_card: 2, recurring_credit_card: 3, check: 4, account_deposit: 5, direct_debit: 6 }, default: :other`

**Associacoes e Recursos Rails:**
- `belongs_to :unity`
- `belongs_to :favorable, polymorphic: true`
- `belongs_to :financial_account`
- `belongs_to :financial_category`
- `belongs_to :cost_center`
- `has_many :cost_installments, dependent: :delete_all`
- `has_many :attachments, as: :attacheable`

**Nested Attributes:**
- `accepts_nested_attributes_for :cost_installments, allow_destroy: true, reject_if: :all_blank`

**Validacoes:**
- `validates_presence_of :description`
- `validates :total_installments, numericality: { greater_than: 0 }, on: :create`

**Metodos Publicos:**
- `def to_s`
- `def update_value`
- `def self.translated_payment_methods`
- `def translated_payment_method`
- `def build_items`
- `def last_cost_installment`
- `def new_cost_installment`
- `def may_edit_items?`

---

### CostAllocation

**Arquivo**: `models/cost_allocation.rb`

**Tabela**: `cost_allocations`

**Colunas do Banco:**
| Coluna | Tipo | Detalhes |
|---|---|---|
| `costable_id` | `integer` | null: false |
| `costable_type` | `string` | null: false |
| `created_at` | `datetime` | null: false |
| `extraction_blob_key` | `string` | - |
| `extraction_response` | `text` | - |
| `project_budget_allocation_id` | `integer` | null: false |
| `rubricable_id` | `integer` | null: false |
| `rubricable_type` | `string` | null: false |
| `updated_at` | `datetime` | null: false |
| `validation_response` | `text` | - |
| `value` | `decimal` | default: "0.0", precision: 22, scale: 11 |

**Associacoes e Recursos Rails:**
- `belongs_to :project_budget_allocation`
- `has_one :project, through: :project_budget_allocation`
- `belongs_to :rubricable, polymorphic: true`
- `belongs_to :costable, polymorphic: true`

**Delegacoes:**
- `delegate :log_date, to: :costable`

**Callbacks:**
- `after_save :extract_nf_job`

**Scopes:**
- `scope :tickets, -> { where(rubricable_type: "Ticket") }`
- `scope :hostings, -> { where(rubricable_type: "Hosting") }`
- `scope :other_rubrics, -> { where(rubricable_type: "OtherRubric") }`
- `scope :grouped_by_costable, -> { group(:costable_id, :costable_type).select("costable_id, costable_type, SUM(value) as total") }`

**Validacoes:**
- `validates :value, numericality: { greater_than: 0.0, less_than_or_equal_to: :pending_value }, on: :create`
- `validates_uniqueness_of :costable_id, scope: %i[ rubricable_type project_budget_allocation_id rubricable_id costable_type ]`
- `validate :value_dont_change, on: :update`

**Metodos Publicos:**
- `def pending_value`
- `def allocated`
- `def allocated_sum`
- `def extract_nf_job`
- `def validate_nf_job`
- `def validate_nf`
- `def validation_json`
- `def alerts`
- `def warnings`
- `def justification`
- `def item_selecionado`
- `def validation_message`
- `def requesting`
- `def rubric`
- `def nf_items`

---

### CostCenter

**Arquivo**: `models/cost_center.rb`

**Tabela**: `cost_centers`

**Colunas do Banco:**
| Coluna | Tipo | Detalhes |
|---|---|---|
| `abbreviation` | `string` | - |
| `created_at` | `datetime` | null: false |
| `name` | `string` | - |
| `project_id` | `integer` | - |
| `status` | `integer` | default: 1 |
| `unity_id` | `integer` | null: false, default: 1 |
| `updated_at` | `datetime` | null: false |

**Includes:**
- `include Loggable`

**Enumerações:**
- `enum :status, { inactive: 0, active: 1 }, default: :active`

**Associacoes e Recursos Rails:**
- `has_rich_text :description`
- `belongs_to :unity`
- `has_many :balances, as: :balanceable`
- `has_many :costs`
- `has_many :imported_costs`
- `has_many :cost_installments`
- `has_many :cost_installment_centers`
- `has_many :projects`
- `has_many :payrolls`
- `belongs_to :project, optional: true`

**Scopes:**
- `scope :search, ->(query) { where("UPPER(name) LIKE :q OR UPPER(abbreviation) LIKE :q", q: "%#{query.upcase.strip}%") if query.present? }`
- `scope :filter_status, ->(status) { where(status: status) if status.present? }`

**Validacoes:**
- `validates :name, :abbreviation, presence: true, uniqueness: { scope: :unity_id }`

**Metodos Publicos:**
- `def self.categories`
- `def category`
- `def to_s`
- `def associations`

---

### CostInstallment

**Arquivo**: `models/cost_installment.rb`

**Tabela**: `cost_installments`

**Colunas do Banco:**
| Coluna | Tipo | Detalhes |
|---|---|---|
| `competence_month` | `integer` | - |
| `competence_year` | `integer` | - |
| `cost_id` | `integer` | null: false |
| `created_at` | `datetime` | null: false |
| `date` | `date` | - |
| `description` | `string` | default: "" |
| `discount` | `decimal` | default: "0.0", precision: 22, scale: 2 |
| `due_date` | `date` | - |
| `extraction_blob_key` | `string` | - |
| `extraction_response` | `text` | - |
| `fees` | `decimal` | default: "0.0", precision: 22, scale: 2 |
| `financial_account_id` | `integer` | - |
| `financial_category_id` | `integer` | - |
| `paid_value` | `decimal` | default: "0.0", precision: 22, scale: 2 |
| `pay_date` | `date` | - |
| `payment_method` | `integer` | default: 0 |
| `penalty` | `decimal` | default: "0.0", precision: 22, scale: 2 |
| `request_date` | `date` | - |
| `status` | `integer` | default: 0 |
| `unity_id` | `integer` | null: false, default: 1 |
| `updated_at` | `datetime` | null: false |
| `user_id` | `integer` | - |
| `validation_response` | `text` | - |
| `value` | `decimal` | default: "0.0", precision: 22, scale: 2 |

**Includes:**
- `include Loggable`
- `include FileValidatable`

**Enumerações:**
- `enum :status, { paid: 0, expected: 1, requested: 2, canceled: 3 }, default: :expected`
- `enum :payment_method, { other: 0, bank_slip: 1, single_credit_card: 2, recurring_credit_card: 3, check: 4, account_deposit: 5, direct_debit: 6 }`

**Associacoes e Recursos Rails:**
- `belongs_to :unity`
- `belongs_to :cost`
- `belongs_to :user, optional: true`
- `belongs_to :financial_account, optional: true`
- `belongs_to :financial_category, optional: true`
- `has_one :cost_center, through: :cost`
- `has_many :cost_installment_centers, dependent: :delete_all`
- `has_many :balances, as: :responsable`
- `has_one_attached :bank_receipt`

**Nested Attributes:**
- `accepts_nested_attributes_for :cost_installment_centers, reject_if: :all_blank, allow_destroy: true`

**Delegacoes:**
- `delegate :favorable, to: :cost`

**Callbacks:**
- `before_validation :set_values`
- `after_save :fill_description`
- `before_save :register_status_change`
- `after_commit :update_cost_total`
- `after_commit :persist_balance, if: :paid?`

**Scopes:**
- `scope :by_status, ->(status) { where(status: status) if status.present? }`
- `scope :by_description, ->(str) { where("UPPER(cost_installments.description) LIKE ?", "%#{str.upcase.strip}%") if str.present? }`
- `scope :by_start_date, ->(date) { where("date >= ?", date) if date.present? }`
- `scope :by_end_date, ->(date) { where("date <= ?", date) if date.present? }`
- `scope :by_financial_account, ->(id) { where(financial_account_id: id) if id.present? }`
- `scope :by_cost_center, ->(id) { where(costs: { cost_center_id: id }).joins(:cost) if id.present? }`
- `scope :by_company, ->(id) { where(costs: { favorable_type: "Company", favorable_id: id }).joins(:cost) if id.present? }`
- `scope :by_person, ->(id) { where(costs: { favorable_type: "Person", favorable_id: id }).joins(:cost) if id.present? }`

**Validacoes:**
- `validates_presence_of :date`
- `validates_presence_of :request_date, :due_date, :financial_account, :financial_category, if: :requested?`
- `validates_presence_of :pay_date, if: :paid?`
- `validates :value, numericality: { greater_than: 0.0 }`
- `validates :paid_value, numericality: { greater_than: 0.0 }, if: :paid?`
- `validate :cost_centers_sum, if: :paid?`
- `validate :paid_cant_change, on: :update`

**Metodos Publicos:**
- `def to_s`
- `def competence`
- `def self.translated_statuses`
- `def self.translated_payment_methods`
- `def translated_status`
- `def translated_payment_method`
- `def cancel`
- `def undo`
- `def set_for_requisition`
- `def set_for_payment`
- `def log_date`
- `def log_value`
- `def undoable?`
- `def payable?`
- `def calculate_paid_value`
- `def undo_message`
- `def all_cost_allocations`

---

### CostInstallmentCenter

**Arquivo**: `models/cost_installment_center.rb`

**Tabela**: `cost_installment_centers`

**Colunas do Banco:**
| Coluna | Tipo | Detalhes |
|---|---|---|
| `cost_center_id` | `integer` | null: false |
| `cost_installment_id` | `integer` | null: false |
| `created_at` | `datetime` | null: false |
| `unity_id` | `integer` | null: false, default: 1 |
| `updated_at` | `datetime` | null: false |
| `value` | `decimal` | default: "0.0", precision: 22, scale: 2 |

**Associacoes e Recursos Rails:**
- `belongs_to :unity`
- `belongs_to :cost_installment`
- `belongs_to :cost_center`
- `has_many :cost_allocations, as: :costable`

**Delegacoes:**
- `delegate :date, :paid?, :expected?, :requested?, :translated_status, :description, :favorable, :warnings, :nf_items, :financial_account, :financial_category, to: :cost_installment`

**Scopes:**
- `scope :by_cost_center, ->(id) { where(cost_center_id: id) if id.present? }`
- `scope :by_start_date, ->(date) { where("cost_installments.date >= ?", date) if date.present? }`
- `scope :by_end_date, ->(date) { where("cost_installments.date <= ?", date) if date.present? }`
- `scope :by_status, ->(st) { where(cost_installments: { status: st }) if st.present? }`
- `scope :by_financial_account, ->(id) { where(cost_installments: { financial_account_id: id }) if id.present? }`

**Metodos Publicos:**
- `def to_s`

---

### CostSpreadsheet

**Arquivo**: `models/cost_spreadsheet.rb`

**Tabela**: `cost_spreadsheets`

**Colunas do Banco:**
| Coluna | Tipo | Detalhes |
|---|---|---|
| `created_at` | `datetime` | null: false |
| `updated_at` | `datetime` | null: false |
| `user_id` | `integer` | null: false |

**Associacoes e Recursos Rails:**
- `belongs_to :user`
- `has_many :imported_costs, dependent: :delete_all`
- `has_one_attached :attachment`

**Nested Attributes:**
- `accepts_nested_attributes_for :imported_costs, reject_if: :all_blank`

**Delegacoes:**
- `delegate :import, to: :parser`

**Validacoes:**
- `validates :attachment, attached: true`
- `validates :attachment, content_type: { in: [ "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet", "application/vnd.ms-excel" ], spoofing_protection: true }`
- `validates :attachment, size: { less_than_or_equal_to: 10.megabytes }`
- `validate :columns_from_attachment`

**Metodos Publicos:**
- `def parser`

---

### ExecutingTeam

**Arquivo**: `models/executing_team.rb`

**Tabela**: `executing_teams`

**Colunas do Banco:**
| Coluna | Tipo | Detalhes |
|---|---|---|
| `charges` | `decimal` | precision: 22, scale: 2 |
| `created_at` | `datetime` | null: false |
| `destination` | `string` | - |
| `hours` | `decimal` | precision: 22, scale: 2 |
| `institution` | `string` | - |
| `item` | `integer` | - |
| `justification` | `string` | - |
| `kind` | `string` | - |
| `level` | `string` | - |
| `member` | `integer` | - |
| `name` | `string` | - |
| `period` | `integer` | - |
| `person_id` | `integer` | - |
| `revenue` | `string` | - |
| `spreadsheet_attachment_id` | `integer` | null: false |
| `subtotal` | `decimal` | precision: 22, scale: 2 |
| `total` | `decimal` | precision: 22, scale: 2 |
| `unit_value` | `decimal` | precision: 22, scale: 2 |
| `updated_at` | `datetime` | null: false |
| `value` | `decimal` | precision: 22, scale: 2 |

**Associacoes e Recursos Rails:**
- `belongs_to :spreadsheet_attachment`
- `has_one :project, through: :spreadsheet_attachment`
- `belongs_to :person, optional: true`
- `has_many :team_budget_allocations`

**Callbacks:**
- `after_update :associate_similar`

**Scopes:**
- `scope :grouped_by_member, -> { group(:member).select( "*, SUM(total) as total_by_member, SUM(period) as total_period, AVG(hours) as hours_average, GROUP_CONCAT(item) as items" ) }`
- `scope :with_revenue, -> { where("UPPER(revenue) = 'SIM'") }`
- `scope :unassociated, -> { where(person_id: nil) }`
- `scope :filter_name, ->(str) { where("UPPER(name) LIKE :q", q: "%#{str.upcase.strip}%") if str.present? }`
- `scope :filter_item, ->(str) { where("item = ?", str) if str.present? }`
- `scope :filter_member, ->(str) { where("member = ?", str) if str.present? }`

---

### FinancialAccount

**Arquivo**: `models/financial_account.rb`

**Tabela**: `financial_accounts`

**Colunas do Banco:**
| Coluna | Tipo | Detalhes |
|---|---|---|
| `abbreviation` | `string` | - |
| `account` | `string` | - |
| `agency` | `string` | - |
| `bank_id` | `integer` | - |
| `company_id` | `integer` | - |
| `created_at` | `datetime` | null: false |
| `expense_restriction` | `boolean` | default: false |
| `kind` | `integer` | - |
| `name` | `string` | - |
| `status` | `integer` | default: 1 |
| `unity_id` | `integer` | null: false, default: 1 |
| `updated_at` | `datetime` | null: false |

**Includes:**
- `include Loggable`

**Enumerações:**
- `enum :status, { inactive: 0, active: 1 }, default: :active`
- `enum :kind, { banking: 0, graphic: 1 }, default: :graphic`

**Associacoes e Recursos Rails:**
- `belongs_to :unity`
- `belongs_to :company`
- `belongs_to :bank, optional: true`
- `has_many :projects`
- `has_many :cost_installments`
- `has_many :transfers_to, foreign_key: :to_account_id, class_name: "Transfer"`
- `has_many :transfers_from, foreign_key: :from_account_id, class_name: "Transfer"`
- `has_many :costs`
- `has_many :payrolls`
- `has_many :balances, as: :balanceable`
- `has_rich_text :description`

**Scopes:**
- `scope :search, ->(query) { where("UPPER(name) LIKE :q OR UPPER(abbreviation) LIKE :q", q: "%#{query.upcase.strip}%") if query.present? }`
- `scope :filter_status, ->(status) { where(status:) if status.present? }`
- `scope :filter_kind, ->(kind) { where(kind:) if kind.present? }`

**Validacoes:**
- `validates :name, :abbreviation, presence: true, uniqueness: { scope: :unity_id }`
- `validates :agency, :account, :bank, presence: true, if: :banking?`

**Metodos Publicos:**
- `def self.categories`
- `def self.kind_types`
- `def kind_type`
- `def category`
- `def to_s`
- `def associations`

---

### FinancialCategory

**Arquivo**: `models/financial_category.rb`

**Tabela**: `financial_categories`

**Colunas do Banco:**
| Coluna | Tipo | Detalhes |
|---|---|---|
| `created_at` | `datetime` | null: false |
| `description` | `string` | default: "" |
| `financial_category_id` | `integer` | - |
| `kind` | `integer` | - |
| `name` | `string` | null: false |
| `position` | `integer` | - |
| `subcategory` | `boolean` | default: false |
| `unity_id` | `integer` | null: false, default: 1 |
| `updated_at` | `datetime` | null: false |

**Enumerações:**
- `enum :kind, { income: 0, expense: 1 }`

**Associacoes e Recursos Rails:**
- `belongs_to :unity`
- `belongs_to :financial_category, optional: true`
- `has_many :financial_categories`
- `has_many :projects`
- `has_many :costs`
- `has_many :cost_installments`
- `has_many :payrolls`

**Scopes:**
- `scope :root, -> { where(subcategory: false) }`
- `scope :subcategory, -> { where(subcategory: true) }`

**Validacoes:**
- `validates :name, :kind, presence: true`
- `validates :name, uniqueness: { scope: [ :kind, :financial_category_id, :unity_id ] }`
- `validates :financial_category_id, presence: true, if: :subcategory`
- `validate :root_is_from_same_kind`

**Metodos Publicos:**
- `def self.structured_for`
- `def self.flattened_for_select_for`
- `def to_s`
- `def self.kind_types`
- `def kind_type`
- `def associations`
- `def name_and_category`
- `def transfer_to`

---

### Hosting

**Arquivo**: `models/hosting.rb`

**Tabela**: `hostings`

**Colunas do Banco:**
| Coluna | Tipo | Detalhes |
|---|---|---|
| `created_at` | `datetime` | null: false |
| `description` | `string` | - |
| `destination` | `string` | - |
| `item` | `integer` | - |
| `justification` | `string` | - |
| `number_of_people` | `integer` | - |
| `quantity_by_person` | `integer` | - |
| `revenue` | `string` | - |
| `spreadsheet_attachment_id` | `integer` | null: false |
| `total` | `decimal` | precision: 22, scale: 2 |
| `travel` | `string` | - |
| `unit_value` | `decimal` | precision: 22, scale: 2 |
| `updated_at` | `datetime` | null: false |

**Associacoes e Recursos Rails:**
- `belongs_to :spreadsheet_attachment`
- `has_many :cost_allocations, as: :rubricable`

**Scopes:**
- `scope :with_revenue, -> { where("UPPER(revenue) = 'SIM'") }`

**Metodos Publicos:**
- `def to_s`
- `def rubric`

---

### ImportedCost

**Arquivo**: `models/imported_cost.rb`

**Tabela**: `imported_costs`

**Colunas do Banco:**
| Coluna | Tipo | Detalhes |
|---|---|---|
| `cost_center_id` | `integer` | null: false |
| `cost_center_s` | `string` | default: "" |
| `cost_spreadsheet_id` | `integer` | - |
| `created_at` | `datetime` | null: false |
| `date` | `date` | - |
| `description` | `string` | - |
| `discount` | `decimal` | default: "0.0", precision: 22, scale: 2 |
| `extraction_blob_key` | `string` | - |
| `extraction_response` | `text` | - |
| `favorable_id` | `integer` | null: false |
| `favorable_s` | `string` | default: "" |
| `favorable_type` | `string` | null: false |
| `fees` | `decimal` | default: "0.0", precision: 22, scale: 2 |
| `financial_account_id` | `integer` | null: false |
| `financial_account_s` | `string` | default: "" |
| `financial_category_id` | `integer` | null: false |
| `financial_category_s` | `string` | default: "" |
| `observations` | `text` | - |
| `payment_method` | `integer` | - |
| `payment_method_s` | `string` | default: "" |
| `penalty` | `decimal` | default: "0.0", precision: 22, scale: 2 |
| `status` | `integer` | - |
| `status_s` | `string` | default: "" |
| `unity_id` | `integer` | null: false, default: 1 |
| `updated_at` | `datetime` | null: false |
| `validation_response` | `text` | - |
| `value` | `decimal` | default: "0.0", precision: 22, scale: 2 |

**Includes:**
- `include Loggable`
- `include FileValidatable`

**Enumerações:**
- `enum :status, { paid: 0, expected: 1, requested: 2, canceled: 3 }`
- `enum :payment_method, { other: 0, bank_slip: 1, single_credit_card: 2, recurring_credit_card: 3, check: 4, account_deposit: 5, direct_debit: 6 }`

**Associacoes e Recursos Rails:**
- `belongs_to :unity`
- `belongs_to :cost_spreadsheet, optional: true`
- `belongs_to :favorable, polymorphic: true`
- `belongs_to :cost_center`
- `belongs_to :financial_account`
- `belongs_to :financial_category`
- `has_many :balances, as: :responsable`
- `has_one_attached :bank_receipt`

**Callbacks:**
- `before_save :register_status_change, unless: :new_record?`
- `after_commit :persist_balance, if: :paid?`

**Scopes:**
- `scope :by_status, ->(status) { where(status: status) if status.present? }`
- `scope :by_description, ->(str) { where("UPPER(description) LIKE ?", "%#{str.upcase.strip}%") if str.present? }`
- `scope :by_start_date, ->(date) { where("date >= ?", date) if date.present? }`
- `scope :by_end_date, ->(date) { where("date <= ?", date) if date.present? }`
- `scope :by_financial_account, ->(id) { where(financial_account_id: id) if id.present? }`
- `scope :by_cost_center, ->(id) { where(cost_center_id: id) if id.present? }`
- `scope :by_company, ->(id) { where(favorable_type: "Company", favorable_id: id) if id.present? }`
- `scope :by_person, ->(id) { where(favorable_type: "Person", favorable_id: id) if id.present? }`

**Validacoes:**
- `validates_presence_of :date, :description, :status, :cost_center_id, :financial_account_id, :financial_category_id`
- `validates :value, numericality: { greater_than: 0.0 }`
- `validates_numericality_of :penalty, :fees, :discount, allow_blank: true`
- `validate :paid_cant_change, on: :update`
- `validates :nf, content_type: :pdf`
- `validates :bank_receipt, content_type: :pdf`

**Metodos Publicos:**
- `def self.translated_statuses`
- `def self.translated_payment_methods`
- `def to_s`
- `def translated_status`
- `def translated_payment_method`
- `def register_status_change`
- `def undo`
- `def undo_message`

---

### IncomeInstallment

**Arquivo**: `models/income_installment.rb`

**Tabela**: `income_installments`

**Colunas do Banco:**
| Coluna | Tipo | Detalhes |
|---|---|---|
| `billing_date` | `date` | - |
| `cancellation_date` | `date` | - |
| `created_at` | `datetime` | null: false |
| `date` | `date` | null: false |
| `description` | `string` | default: "" |
| `due_date` | `date` | - |
| `nf_number` | `string` | - |
| `nf_url` | `string` | - |
| `number` | `integer` | default: 1 |
| `percentage` | `decimal` | default: "0.0", precision: 22, scale: 11 |
| `project_id` | `integer` | null: false |
| `receipt_date` | `date` | - |
| `received_cancellation_date` | `date` | - |
| `received_value` | `decimal` | default: "0.0", precision: 22, scale: 2 |
| `request_date` | `date` | - |
| `status` | `integer` | default: 0 |
| `unity_id` | `integer` | null: false, default: 1 |
| `updated_at` | `datetime` | null: false |
| `user_id` | `integer` | - |
| `value` | `decimal` | default: "0.0", precision: 22, scale: 2 |

**Includes:**
- `include Loggable`

**Enumerações:**
- `enum :status, { expected: 0, billed: 1, received: 2, canceled: 3, requested: 4 }, default: :expected`

**Associacoes e Recursos Rails:**
- `belongs_to :unity`
- `belongs_to :project`
- `belongs_to :user, optional: true`
- `has_one :financial_account, through: :project`
- `has_one :financial_category, through: :project`
- `has_one :cost_center, through: :project`
- `has_one_attached :nf`
- `has_many :balances, as: :responsable`

**Callbacks:**
- `before_save :register_status_change`
- `before_validation :format_site, if: -> { nf_url.present? }`

**Scopes:**
- `scope :by_status, ->(status) { where(status: status) if status.present? }`
- `scope :by_project, ->(project_id) { where(project_id: project_id) if project_id.present? }`
- `scope :by_company, ->(company_id) { joins(:project).where(projects: { company_id: company_id }) if company_id.present? }`
- `scope :by_cost_center, ->(cc_id) { joins(:project).where(projects: { cost_center_id: cc_id }) if cc_id.present? }`
- `scope :by_start_date, ->(date) { where("date >= ?", date) if date.present? }`
- `scope :by_end_date, ->(date) { where("date <= ?", date) if date.present? }`
- `scope :by_financial_account, ->(id) { joins(:project).where(projects: { financial_account_id: id }) if id.present? }`

**Validacoes:**
- `validates :nf, content_type: :pdf`
- `validates_presence_of :date`
- `validates_presence_of :request_date, if: :requested?`
- `validates_presence_of :billing_date, :nf_number, :due_date, if: :billed?`
- `validates_presence_of :receipt_date, if: :received?`
- `validates_presence_of :cancellation_date, if: :is_being_cancelled?`
- `validates_presence_of :received_cancellation_date, if: :is_being_cancelled?`
- `validates :value, numericality: { greater_than: 0.0 }`
- `validates :received_value, numericality: { greater_than: 0.0 }, if: :received?`
- `validate :dont_change_unless_expected`

**Metodos Publicos:**
- `def to_s`
- `def receive`
- `def undo`
- `def self.status_colors`
- `def status_color`
- `def self.translated_statuses`
- `def translated_status`
- `def billable?`
- `def not_expected?`
- `def log_date`
- `def log_value`
- `def set_for_requisition`
- `def set_for_billing`
- `def set_for_receiving`
- `def set_for_cancellation`
- `def cancel_button_text`
- `def is_being_cancelled?`
- `def calculate_percentage`
- `def favorable`

---

### OtherRubric

**Arquivo**: `models/other_rubric.rb`

**Tabela**: `other_rubrics`

**Colunas do Banco:**
| Coluna | Tipo | Detalhes |
|---|---|---|
| `created_at` | `datetime` | null: false |
| `description` | `string` | - |
| `destination` | `string` | - |
| `group` | `integer` | - |
| `item` | `integer` | - |
| `justification` | `string` | - |
| `kind` | `string` | - |
| `quantity` | `decimal` | precision: 22, scale: 2 |
| `revenue` | `string` | - |
| `rubric` | `string` | - |
| `spreadsheet_attachment_id` | `integer` | null: false |
| `total` | `decimal` | precision: 22, scale: 2 |
| `unit_value` | `decimal` | precision: 22, scale: 2 |
| `updated_at` | `datetime` | null: false |

**Associacoes e Recursos Rails:**
- `belongs_to :spreadsheet_attachment`
- `has_many :cost_allocations, as: :rubricable`

**Metodos Publicos:**
- `def to_s`

---

### Payroll

**Arquivo**: `models/payroll.rb`

**Tabela**: `payrolls`

**Colunas do Banco:**
| Coluna | Tipo | Detalhes |
|---|---|---|
| `competence` | `string` | - |
| `cost_center_id` | `integer` | null: false |
| `created_at` | `datetime` | null: false |
| `date` | `date` | - |
| `description` | `string` | - |
| `financial_account_id` | `integer` | null: false |
| `financial_category_id` | `integer` | null: false |
| `observations` | `text` | - |
| `payroll_spreadsheet_id` | `integer` | null: false |
| `person_id` | `integer` | null: false |
| `unity_id` | `integer` | null: false, default: 1 |
| `updated_at` | `datetime` | null: false |
| `value` | `decimal` | precision: 22, scale: 2 |

**Associacoes e Recursos Rails:**
- `belongs_to :payroll_spreadsheet`
- `belongs_to :person`
- `belongs_to :cost_center`
- `belongs_to :financial_account`
- `belongs_to :financial_category`
- `belongs_to :unity`

**Scopes:**
- `scope :search, ->(query) { where("competence LIKE ?", "%#{query.strip}%") if query.present? }`
- `scope :by_cost_center, ->(id) { where(cost_center_id: id) if id.present? }`
- `scope :by_financial_account, ->(id) { where(financial_account_id: id) if id.present? }`
- `scope :by_person, ->(id) { where(person_id: id) if id.present? }`
- `scope :by_financial_category, ->(id) { where(financial_category_id: id) if id.present? }`
- `scope :by_start_date, ->(date) { where("date >= ?", date) if date.present? }`
- `scope :by_end_date, ->(date) { where("date <= ?", date) if date.present? }`
- `scope :by_start_competence, ->(cmp) { where("CAST(competence as integer) >= ?", cmp) if cmp.present? }`
- `scope :by_end_competence, ->(cmp) { where("CAST(competence as integer) <= ?", cmp) if cmp.present? }`
- `scope :group_by_financial_category, -> { joins(:financial_category).group(group_columns).select(group_select_columns) }`
- `scope :group_by_financial_subcategory, -> { joins(:financial_category).group(subgroup_columns).select(group_select_columns) }`

**Metodos Publicos:**
- `def to_s`
- `def translated_status`
- `def favorable`

---

### PayrollSpreadsheet

**Arquivo**: `models/payroll_spreadsheet.rb`

**Tabela**: `payroll_spreadsheets`

**Colunas do Banco:**
| Coluna | Tipo | Detalhes |
|---|---|---|
| `competence` | `string` | - |
| `created_at` | `datetime` | null: false |
| `number` | `integer` | - |
| `title` | `string` | default: "" |
| `unity_id` | `integer` | null: false, default: 1 |
| `updated_at` | `datetime` | null: false |
| `user_id` | `integer` | null: false |

**Associacoes e Recursos Rails:**
- `belongs_to :unity`
- `belongs_to :user`
- `has_many :payrolls, dependent: :delete_all`
- `has_many :financial_accounts, -> { distinct }, through: :payrolls`
- `has_many :cost_centers, -> { distinct }, through: :payrolls`
- `has_many :people, -> { distinct }, through: :payrolls`
- `has_many :financial_categories, -> { distinct }, through: :payrolls`
- `has_one_attached :attachment`

**Callbacks:**
- `before_save :set_number`

**Scopes:**
- `scope :search, ->(query) { where("competence LIKE ?", "%#{query.strip}%") if query.present? }`
- `scope :filter_title, ->(query) { where("title LIKE ?", "%#{query.strip}%") if query.present? }`
- `scope :filter_ids, ->(ids) { where(id: ids) if ids.present? }`

**Validacoes:**
- `validates :attachment, attached: true`
- `validates :attachment, content_type: { in: [ "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet", "application/vnd.ms-excel" ], spoofing_protection: true }`
- `validates :attachment, size: { less_than_or_equal_to: 10.megabytes }`
- `validates_presence_of :competence, :title`
- `validate :sheets_from_attachment, if: :no_other_validation_errors?`
- `validate :columns_from_spreadsheet, if: :no_other_validation_errors?`
- `validate :competence_presence_on_spreadsheet, if: :no_other_validation_errors?`
- `validate :competence_format_on_spreadsheet, if: :no_other_validation_errors?`
- `validate :date_presence_on_spreadsheet, if: :no_other_validation_errors?`
- `validate :cpf_presence_on_spreadsheet, if: :no_other_validation_errors?`
- `validate :name_presence_on_spreadsheet, if: :no_other_validation_errors?`
- `validate :value_numericality_on_spreadsheet, if: :no_other_validation_errors?`
- `validate :description_presence_on_spreadsheet, if: :no_other_validation_errors?`
- `validate :cost_center_presence_on_spreadsheet, if: :no_other_validation_errors?`
- `validate :financial_account_presence_on_spreadsheet, if: :no_other_validation_errors?`
- `validate :financial_subcategory_presence_on_spreadsheet, if: :no_other_validation_errors?`
- `validate :person_cpf_association, if: :no_other_validation_errors?`
- `validate :cost_center_association, if: :no_other_validation_errors?`
- `validate :financial_account_association, if: :no_other_validation_errors?`
- `validate :financial_category_association, if: :no_other_validation_errors?`
- `validate :financial_subcategory_association, if: :no_other_validation_errors?`

**Metodos Publicos:**
- `def to_s`
- `def parser`
- `def import`
- `def no_other_validation_errors?`

---

### Person

**Arquivo**: `models/person.rb`

**Tabela**: `people`

**Colunas do Banco:**
| Coluna | Tipo | Detalhes |
|---|---|---|
| `address` | `string` | - |
| `address_city` | `string` | - |
| `address_complement` | `string` | - |
| `address_district` | `string` | - |
| `address_number` | `integer` | - |
| `address_state` | `string` | - |
| `cellphone` | `string` | - |
| `cep` | `string` | - |
| `company_id` | `integer` | - |
| `complete_name` | `string` | null: false |
| `country` | `string` | - |
| `cpf` | `string` | null: false |
| `created_at` | `datetime` | null: false |
| `cv_url` | `string` | - |
| `date_of_birth` | `date` | - |
| `department` | `string` | default: "" |
| `email` | `string` | - |
| `incomplete` | `boolean` | default: false |
| `lattes_updated_at` | `date` | - |
| `linkedin_url` | `string` | - |
| `name` | `string` | null: false |
| `person_type_id` | `integer` | - |
| `phone` | `string` | - |
| `position` | `string` | - |
| `rg` | `string` | - |
| `rg_date` | `date` | - |
| `rg_issuer` | `string` | - |
| `role` | `integer` | - |
| `sigitec_register` | `boolean` | default: false |
| `status` | `integer` | - |
| `supplier` | `boolean` | default: false |
| `team_research_unit` | `boolean` | default: false |
| `unity_id` | `integer` | null: false, default: 1 |
| `updated_at` | `datetime` | null: false |

**Enumerações:**
- `enum :status, { inactive: 0, active: 1 }, default: :active`
- `enum :role, { representative: 0, billing_contact: 1, focal_point: 2 }`

**Associacoes e Recursos Rails:**
- `belongs_to :unity`
- `belongs_to :person_type, optional: true`
- `belongs_to :company, optional: true`
- `has_many :costs, as: :favorable`
- `has_many :projects`
- `has_many :payrolls`
- `has_rich_text :description`
- `has_rich_text :observations`
- `has_one_attached :image do |img|`

**Scopes:**
- `scope :search, ->(query) { return if query.blank? where("UPPER(complete_name) LIKE :q OR UPPER(name) LIKE :q OR cpf LIKE :q", q: "%#{query.upcase.strip}%") }`
- `scope :filter_status, ->(status) { where(status:) if status.present? }`
- `scope :filter_type, ->(type_id) { where(person_type_id: PersonType.where(person_type_id: type_id).ids << type_id) if type_id.present? }`
- `scope :filter_company, ->(id) { where(company_id: id) if id.present? }`
- `scope :supplier, -> { where(supplier: true) }`
- `scope :cpf_present, -> { where("cpf <> ''") }`
- `scope :incomplete, ->(str) { where(incomplete: true) if str.present? }`

**Validacoes:**
- `validates :name, :complete_name, presence: true`
- `validates :cpf, format: { with: /\A\b\d{3}\.\d{3}\.\d{3}\-\d{2}\b\z/, message: "deve estar no formato DDD.DDD.DDD-DD" }, uniqueness: { scope: :unity_id }, allow_blank: true`
- `validates :image, content_type: { in: [ :png, :jpeg ], spoofing_protection: true }`

**Metodos Publicos:**
- `def self.order_by`
- `def self.categories`
- `def self.translated_roles`
- `def translated_role`
- `def category`
- `def to_s`
- `def abbreviation`
- `def complete_address`
- `def contact`
- `def any_contact?`
- `def any_document?`
- `def associations`
- `def name_upcase`
- `def cpf_digits`
- `def id_cpf_digits`
- `def self_and_name`
- `def formatted_name`

---

### PersonType

**Arquivo**: `models/person_type.rb`

**Tabela**: `person_types`

**Colunas do Banco:**
| Coluna | Tipo | Detalhes |
|---|---|---|
| `created_at` | `datetime` | null: false |
| `description` | `string` | default: "" |
| `name` | `string` | null: false |
| `person_type_id` | `integer` | - |
| `position` | `integer` | - |
| `subtype` | `boolean` | default: false |
| `unity_id` | `integer` | null: false, default: 1 |
| `updated_at` | `datetime` | null: false |

**Associacoes e Recursos Rails:**
- `belongs_to :unity`
- `belongs_to :person_type, optional: true`
- `has_many :person_types`
- `has_many :people`

**Scopes:**
- `scope :subtype, -> { where(subtype: true) }`
- `scope :root, -> { where(subtype: false) }`
- `scope :ordered, -> { order(position: :asc) }`

**Validacoes:**
- `validates :name, presence: true, uniqueness: { scope: [ :person_type_id, :unity_id ] }`
- `validates :person_type_id, presence: true, if: :subtype`
- `validate :person_is_not_root, on: :update`

**Metodos Publicos:**
- `def self.flattened_for_select_for`
- `def self.structured_for`
- `def to_s`
- `def associations`
- `def transfer_to`

---

### Project

**Arquivo**: `models/project.rb`

**Tabela**: `projects`

**Colunas do Banco:**
| Coluna | Tipo | Detalhes |
|---|---|---|
| `code` | `string` | default: "" |
| `company_id` | `integer` | null: false |
| `cost_center_id` | `integer` | - |
| `created_at` | `datetime` | null: false |
| `date_reminder` | `date` | - |
| `description_reminder` | `string` | default: "" |
| `end_date` | `date` | - |
| `financial_account_id` | `integer` | - |
| `financial_category_id` | `integer` | - |
| `name` | `string` | default: "" |
| `person_id` | `integer` | - |
| `start_date` | `date` | - |
| `status` | `integer` | - |
| `title` | `string` | - |
| `total_installments` | `integer` | default: 0 |
| `unity_id` | `integer` | null: false, default: 1 |
| `updated_at` | `datetime` | null: false |
| `user_id` | `integer` | - |
| `value` | `decimal` | default: "0.0", precision: 22, scale: 2 |

**Enumerações:**
- `enum :status, { opportunity: 0, in_progress: 1, lost: 2, closed: 3, canceled: 4 }, default: :opportunity`

**Associacoes e Recursos Rails:**
- `belongs_to :unity`
- `belongs_to :company`
- `belongs_to :financial_category, optional: true`
- `belongs_to :cost_center`
- `belongs_to :financial_account`
- `belongs_to :person, optional: true`
- `belongs_to :user, optional: true`
- `has_many :income_installments, dependent: :destroy`
- `has_many :relevant_facts, dependent: :destroy`
- `has_many :people, through: :company`
- `has_many :imported_costs, through: :cost_center`
- `has_many :costs, through: :cost_center`
- `has_many :cost_installments, through: :costs`
- `has_many :cost_installment_centers, through: :cost_installments`
- `has_many :attachments, as: :attacheable`
- `has_many :spreadsheet_attachments`
- `has_many :project_budget_allocations`
- `has_many :transfers_from, through: :financial_account`
- `has_rich_text :object_description`
- `has_rich_text :delivery_description`

**Nested Attributes:**
- `accepts_nested_attributes_for :income_installments, reject_if: :all_blank, allow_destroy: true`

**Scopes:**
- `scope :with_reminder, -> { where("date_reminder <> '' AND description_reminder <> ''") }`
- `scope :by_status, ->(status) { where(status: status) if status.present? }`
- `scope :by_company, ->(company_id) { where(company_id: company_id) if company_id.present? }`
- `scope :search, ->(query) { where("UPPER(title) LIKE ?", "%#{query.upcase.strip}%") if query.present? }`

**Validacoes:**
- `validates_presence_of :title`
- `validates :value, numericality: { greater_than: 0.0 }`
- `validates :total_installments, numericality: { greater_than: 0 }`
- `validates :total_installments, numericality: { less_than: 100 }, if: :installments_empty?`
- `validate :income_installments_sum, on: :update`
- `validate :income_installments_exclusion, on: :update`

**Metodos Publicos:**
- `def to_s`
- `def self.translated_statuses`
- `def translated_status`
- `def build_installments`
- `def update_total_installments`
- `def update_installments_fields`
- `def destroyable?`
- `def months`
- `def installments_empty?`
- `def all_costs`

---

### ProjectBudgetAllocation

**Arquivo**: `models/project_budget_allocation.rb`

**Tabela**: `project_budget_allocations`

**Colunas do Banco:**
| Coluna | Tipo | Detalhes |
|---|---|---|
| `created_at` | `datetime` | null: false |
| `project_id` | `integer` | null: false |
| `spreadsheet_attachment_id` | `integer` | null: false |
| `updated_at` | `datetime` | null: false |
| `user_id` | `integer` | null: false |
| `version` | `integer` | null: false, default: 0 |

**Associacoes e Recursos Rails:**
- `belongs_to :project`
- `has_one :cost_center, through: :project`
- `belongs_to :spreadsheet_attachment`
- `has_many :tickets, through: :spreadsheet_attachment`
- `has_many :hostings, through: :spreadsheet_attachment`
- `has_many :other_rubrics, through: :spreadsheet_attachment`
- `belongs_to :user`
- `has_many :team_budget_allocations, dependent: :delete_all`
- `has_many :cost_allocations, dependent: :delete_all`

**Delegacoes:**
- `delegate :set_executing_people, to: :spreadsheet_attachment`

**Callbacks:**
- `after_create :set_executing_people`
- `after_create :set_team_budget_payrolls`

**Validacoes:**
- `validates_presence_of :version`
- `validates_uniqueness_of :version, scope: :spreadsheet_attachment_id`
- `validate :project_start_date_presence`

**Metodos Publicos:**
- `def to_s`
- `def filename`
- `def export`
- `def rubrics`
- `def available_rubrics_for`
- `def pending_costs`
- `def payroll_groups`
- `def payrolls`
- `def update_person_for`
- `def executing_teams`
- `def update_team_budget_allocations`

---

### RelevantFact

**Arquivo**: `models/relevant_fact.rb`

**Tabela**: `relevant_facts`

**Colunas do Banco:**
| Coluna | Tipo | Detalhes |
|---|---|---|
| `created_at` | `datetime` | null: false |
| `date` | `date` | - |
| `description` | `text` | default: "" |
| `done` | `boolean` | default: false |
| `fact_type` | `integer` | - |
| `project_id` | `integer` | null: false |
| `updated_at` | `datetime` | null: false |

**Enumerações:**
- `enum :fact_type, { opportunity_started: 0, proposal_sent: 1, opportunity_lost: 2, contract_negotiation_started: 3, contract_signed: 4, contract_canceled: 5, contract_closed: 6, contract_amendment: 7, milestone_delivered: 8, other: 9 }`

**Associacoes e Recursos Rails:**
- `belongs_to :project`

**Scopes:**
- `scope :search, ->(query) { where("UPPER(description) LIKE ?", "%#{query.upcase.strip}%") if query.present? }`

**Validacoes:**
- `validates :date, :fact_type, :description, presence: true`

**Metodos Publicos:**
- `def self.translated_fact_types`
- `def translated_fact_type`
- `def status_label`

---

### SpreadsheetAttachment

**Arquivo**: `models/spreadsheet_attachment.rb`

**Tabela**: `spreadsheet_attachments`

**Colunas do Banco:**
| Coluna | Tipo | Detalhes |
|---|---|---|
| `created_at` | `datetime` | null: false |
| `date` | `date` | - |
| `name` | `string` | default: "" |
| `project_id` | `integer` | null: false |
| `status` | `integer` | default: 0 |
| `total` | `decimal` | default: "0.0", precision: 22, scale: 11 |
| `updated_at` | `datetime` | null: false |
| `user_id` | `integer` | null: false |

**Enumerações:**
- `enum :status, { uploaded: 0, imported: 1 }, default: :uploaded`

**Associacoes e Recursos Rails:**
- `belongs_to :project`
- `belongs_to :user`
- `has_many :executing_teams, dependent: :delete_all`
- `has_many :tickets, dependent: :delete_all`
- `has_many :hostings, dependent: :delete_all`
- `has_many :other_rubrics, dependent: :delete_all`
- `has_many :project_budget_allocations, dependent: :delete_all`
- `has_one_attached :attachment`

**Callbacks:**
- `after_save :update_total`

**Scopes:**
- `scope :by_date, -> { order(date: :desc, created_at: :desc) }`

**Validacoes:**
- `validates :attachment, attached: true`
- `validates :attachment, content_type: { in: [ "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet", "application/vnd.ms-excel" ], spoofing_protection: true }`
- `validates :attachment, size: { less_than_or_equal_to: 10.megabytes }`
- `validates_presence_of :date, :name`
- `validate :sheets_from_attachment, if: :no_other_validation_errors?`
- `validate :columns_from_executing_teams, if: :no_other_validation_errors?`
- `validate :columns_from_tickets, if: :no_other_validation_errors?`
- `validate :columns_from_hostings, if: :no_other_validation_errors?`
- `validate :type_presence_on_executing_teams, if: :no_other_validation_errors?`
- `validate :item_numericality_on_executing_teams, if: :no_other_validation_errors?`
- `validate :member_numericality_on_executing_teams, if: :no_other_validation_errors?`
- `validate :name_presence_on_executing_teams, if: :no_other_validation_errors?`
- `validate :level_presence_on_executing_teams, if: :no_other_validation_errors?`
- `validate :formacao_presence_on_executing_teams, if: :no_other_validation_errors?`
- `validate :institution_presence_on_executing_teams, if: :no_other_validation_errors?`
- `validate :period_numericality_on_executing_teams, if: :no_other_validation_errors?`
- `validate :hours_numericality_on_executing_teams, if: :no_other_validation_errors?`
- `validate :value_numericality_on_executing_teams, if: :no_other_validation_errors?`
- `validate :subtotal_numericality_on_executing_teams, if: :no_other_validation_errors?`
- `validate :total_numericality_on_executing_teams, if: :no_other_validation_errors?`
- `validate :item_numericality_on_tickets, if: :no_other_validation_errors?`
- `validate :travel_presence_on_tickets, if: :no_other_validation_errors?`
- `validate :interval_presence_on_tickets, if: :no_other_validation_errors?`
- `validate :total_numericality_on_tickets, if: :no_other_validation_errors?`
- `validate :item_numericality_on_hostings, if: :no_other_validation_errors?`
- `validate :travel_presence_on_hostings, if: :no_other_validation_errors?`
- `validate :description_presence_on_hostings, if: :no_other_validation_errors?`
- `validate :total_numericality_on_hostings, if: :no_other_validation_errors?`
- `validate :rubric_presence_on_other_rubrics, if: :no_other_validation_errors?`
- `validate :type_presence_on_other_rubrics, if: :no_other_validation_errors?`
- `validate :item_numericality_on_other_rubrics, if: :no_other_validation_errors?`
- `validate :description_presence_on_other_rubrics, if: :no_other_validation_errors?`
- `validate :total_numericality_on_other_rubrics, if: :no_other_validation_errors?`
- `validate :item_uniqueness_on_executing_teams, if: :no_other_validation_errors?`
- `validate :item_uniqueness_on_tickets, if: :no_other_validation_errors?`
- `validate :item_uniqueness_on_hostings, if: :no_other_validation_errors?`
- `validate :item_uniqueness_on_other_rubrics, if: :no_other_validation_errors?`

**Metodos Publicos:**
- `def to_s`
- `def parser`
- `def import`
- `def no_other_validation_errors?`
- `def set_executing_people`
- `def update_total`
- `def calculate_total`
- `def associations`

---

### StatusChange

**Arquivo**: `models/status_change.rb`

**Tabela**: `status_changes`

**Colunas do Banco:**
| Coluna | Tipo | Detalhes |
|---|---|---|
| `changeable_id` | `integer` | null: false |
| `changeable_type` | `string` | null: false |
| `created_at` | `datetime` | null: false |
| `date` | `date` | null: false |
| `label` | `string` | null: false |
| `updated_at` | `datetime` | null: false |
| `user_id` | `integer` | null: false |

**Associacoes e Recursos Rails:**
- `belongs_to :changeable, polymorphic: true`
- `belongs_to :user`

**Validacoes:**
- `validates_presence_of :date, :label`

---

### TeamBudgetAllocation

**Arquivo**: `models/team_budget_allocation.rb`

**Tabela**: `team_budget_allocations`

**Colunas do Banco:**
| Coluna | Tipo | Detalhes |
|---|---|---|
| `competence` | `string` | - |
| `created_at` | `datetime` | null: false |
| `date` | `date` | - |
| `difference` | `decimal` | default: "0.0", precision: 22, scale: 2 |
| `executing_team_id` | `integer` | null: false |
| `period_month` | `integer` | - |
| `person_id` | `integer` | - |
| `project_budget_allocation_id` | `integer` | null: false |
| `updated_at` | `datetime` | null: false |
| `value` | `decimal` | default: "0.0", precision: 22, scale: 2 |

**Associacoes e Recursos Rails:**
- `belongs_to :project_budget_allocation`
- `has_one :project, through: :project_budget_allocation`
- `belongs_to :executing_team`
- `belongs_to :person, optional: true`

**Delegacoes:**
- `delegate :name, :item, :member, :subtotal, :period, to: :executing_team, prefix: :executing_team`

**Scopes:**
- `scope :filter_name, ->(str) { return if str.blank? joins(:executing_team).where("UPPER(executing_teams.name) LIKE :q", q: "%#{str.upcase.strip}%") }`
- `scope :filter_item, ->(str) { joins(:executing_team).where("executing_teams.item = ?", str) if str.present? }`
- `scope :filter_member, ->(str) { joins(:executing_team).where("executing_teams.member = ?", str) if str.present? }`
- `scope :filter_competence, ->(str) { where("competence LIKE :q", q: "%#{str.strip}%") if str.present? }`

**Metodos Publicos:**
- `def project_month`
- `def competence_month`

---

### Ticket

**Arquivo**: `models/ticket.rb`

**Tabela**: `tickets`

**Colunas do Banco:**
| Coluna | Tipo | Detalhes |
|---|---|---|
| `created_at` | `datetime` | null: false |
| `destination` | `string` | - |
| `interval` | `string` | - |
| `item` | `integer` | - |
| `justification` | `string` | - |
| `quantity` | `integer` | - |
| `revenue` | `string` | - |
| `spreadsheet_attachment_id` | `integer` | null: false |
| `total` | `decimal` | precision: 22, scale: 2 |
| `travel` | `string` | - |
| `unit_value` | `decimal` | precision: 22, scale: 2 |
| `updated_at` | `datetime` | null: false |

**Associacoes e Recursos Rails:**
- `belongs_to :spreadsheet_attachment`
- `has_many :cost_allocations, as: :rubricable`

**Scopes:**
- `scope :with_revenue, -> { where("UPPER(revenue) = 'SIM'") }`

**Metodos Publicos:**
- `def to_s`
- `def rubric`

---

### Transfer

**Arquivo**: `models/transfer.rb`

**Tabela**: `transfers`

**Colunas do Banco:**
| Coluna | Tipo | Detalhes |
|---|---|---|
| `created_at` | `datetime` | null: false |
| `date` | `date` | null: false |
| `description` | `string` | default: "" |
| `from_account_id` | `integer` | null: false |
| `status` | `integer` | default: 0 |
| `to_account_id` | `integer` | null: false |
| `to_description` | `string` | default: "" |
| `unity_id` | `integer` | null: false, default: 1 |
| `updated_at` | `datetime` | null: false |
| `user_id` | `integer` | null: false |
| `value` | `decimal` | default: "0.0", precision: 22, scale: 2 |
| `void` | `boolean` | default: false |

**Includes:**
- `include Loggable`

**Enumerações:**
- `enum :status, { paid: 0, expected: 1 }, default: :expected, validate: true`

**Associacoes e Recursos Rails:**
- `belongs_to :unity`
- `belongs_to :from_account, class_name: "FinancialAccount"`
- `belongs_to :to_account, class_name: "FinancialAccount"`
- `belongs_to :user, optional: true`
- `has_many :responsables, as: :responsable, class_name: "Balance"`
- `has_many :cost_allocations, as: :costable`

**Callbacks:**
- `after_save :log_change`

**Scopes:**
- `scope :active, -> { where(void: false) }`
- `scope :by_description, ->(str) { where("UPPER(description) LIKE ?", "%#{str.upcase.strip}%") if str.present? }`
- `scope :by_account, ->(id) { where("from_account_id = :q OR to_account_id = :q", q: id) if id.present? }`
- `scope :by_from_account, ->(id) { where(from_account_id: id) if id.present? }`
- `scope :by_to_account, ->(id) { where(to_account_id: id) if id.present? }`
- `scope :by_date, ->(str) { where(date: str) if str.present? }`
- `scope :by_start_date, ->(date) { where("date >= ?", date) if date.present? }`
- `scope :by_end_date, ->(date) { where("date <= ?", date) if date.present? }`
- `scope :by_status, ->(status) { where(status: status) if status.present? }`
- `scope :by_month_year, ->(month, year) do`

**Validacoes:**
- `validates :date, presence: true`
- `validates :value, numericality: { greater_than: 0.0 }`
- `validate :different_accounts`
- `validate :status_cannot_revert`
- `validate :paid_transfer_is_unchanged, unless: :new_record?`

**Metodos Publicos:**
- `def self.translated_statuses`
- `def to_s`
- `def update_balances`
- `def transfer_balances`
- `def active?`
- `def requested?`
- `def cost_center`
- `def financial_category`
- `def undo`
- `def translated_status`
- `def favorable`
- `def warnings`
- `def pay`
- `def format_as_cost`
- `def format_as_income`

---

### Unity

**Arquivo**: `models/unity.rb`

**Tabela**: `unities`

**Colunas do Banco:**
| Coluna | Tipo | Detalhes |
|---|---|---|
| `created_at` | `datetime` | null: false |
| `name` | `string` | null: false |
| `updated_at` | `datetime` | null: false |

**Includes:**
- `include Loggable`

**Associacoes e Recursos Rails:**
- `has_many :users`

**Callbacks:**
- `before_validation :format_name`

**Validacoes:**
- `validates :name, presence: true, uniqueness: true`

**Metodos Publicos:**
- `def to_s`

---

### User

**Arquivo**: `models/user.rb`

**Tabela**: `users`

**Colunas do Banco:**
| Coluna | Tipo | Detalhes |
|---|---|---|
| `created_at` | `datetime` | null: false |
| `current_sign_in_at` | `datetime` | - |
| `current_sign_in_ip` | `string` | - |
| `email` | `string` | null: false, default: "" |
| `encrypted_password` | `string` | null: false, default: "" |
| `failed_attempts` | `integer` | null: false, default: 0 |
| `last_sign_in_at` | `datetime` | - |
| `last_sign_in_ip` | `string` | - |
| `locked_at` | `datetime` | - |
| `name` | `string` | null: false, default: "" |
| `remember_created_at` | `datetime` | - |
| `reset_password_sent_at` | `datetime` | - |
| `reset_password_token` | `string` | - |
| `role` | `integer` | null: false, default: 0 |
| `sign_in_count` | `integer` | null: false, default: 0 |
| `unity_id` | `integer` | null: false, default: 1 |
| `unlock_token` | `string` | - |
| `updated_at` | `datetime` | null: false |

**Includes:**
- `include Loggable`
- `include Unitadable`

**Enumerações:**
- `enum :role, { geral: 0, responsible: 1, coordinator: 2, administrative: 3, no_role: 4 }`

**Associacoes e Recursos Rails:**
- `has_one_attached :image do |img|`
- `belongs_to :unity`
- `has_many :spreadsheet_attachments`
- `has_many :payroll_spreadsheets`
- `has_many :cost_spreadsheets`

**Scopes:**
- `scope :search, ->(query) { where("UPPER(name) LIKE :q OR UPPER(email) LIKE :q", q: "%#{query.upcase.strip}%") if query.present? }`
- `scope :filter_role, ->(role) { where(role:) if role.present? }`
- `scope :from_unity, ->(id) { where(unity_id: id) if id.present? }`

**Validacoes:**
- `validates_presence_of :name, :role`

**Metodos Publicos:**
- `def to_s`
- `def name_email`
- `def self.categories`
- `def category`
- `def add_role`

---

## Controllers (Camada de Apresentacao)

### AttachmentTypesController

**Arquivo**: `controllers/attachment_types_controller.rb`

**Filtros:**
- `before_action :set_attachment_type, only: %i[edit update destroy]`

**Acoes:**
- `def index`
- `def new`
- `def create`
- `def edit`
- `def update`
- `def destroy`

---

### AttachmentsController

**Arquivo**: `controllers/attachments_controller.rb`

**Filtros:**
- `before_action :set_attachment, except: %i[index new create]`

**Acoes:**
- `def index`
- `def show`
- `def new`
- `def create`
- `def update`
- `def destroy`

---

### BalancesController

**Arquivo**: `controllers/balances_controller.rb`

**Filtros:**
- `before_action :set_balanceable`
- `before_action :set_sortable_table_header_path`

**Acoes:**
- `def index`

---

### CompaniesController

**Arquivo**: `controllers/companies_controller.rb`

**Filtros:**
- `before_action :set_company, only: %i[ show edit update destroy ]`

**Acoes:**
- `def index`
- `def show`
- `def new`
- `def edit`
- `def create`
- `def update`
- `def destroy`

---

### CompanyTypesController

**Arquivo**: `controllers/company_types_controller.rb`

**Filtros:**
- `before_action :set_company_type, only: %i[edit update destroy show transfer]`
- `before_action :set_root_company_types, only: %i[new edit create update]`

**Acoes:**
- `def index`
- `def show`
- `def new`
- `def create`
- `def edit`
- `def update`
- `def destroy`
- `def transfer`

---

### CostAllocationsController

**Arquivo**: `controllers/cost_allocations_controller.rb`

**Filtros:**
- `before_action :set_project_budget_allocation`
- `before_action :set_cost_allocation, only: %i[edit update destroy]`
- `before_action :set_rubrics, only: %i[edit update]`

**Acoes:**
- `def index`
- `def new`
- `def create`
- `def edit`
- `def update`
- `def destroy`

---

### CostCenters::BalancesController

**Arquivo**: `controllers/cost_centers/balances_controller.rb`

**Acoes:**
- `def set_balanceable`
- `def set_sortable_table_header_path`

---

### CostCentersController

**Arquivo**: `controllers/cost_centers_controller.rb`

**Filtros:**
- `before_action :set_cost_center, only: %i[ show edit update destroy ]`

**Acoes:**
- `def index`
- `def show`
- `def new`
- `def edit`
- `def create`
- `def update`
- `def destroy`

---

### CostInstallmentCentersController

**Arquivo**: `controllers/cost_installment_centers_controller.rb`

**Acoes:**
- `def show`

---

### CostInstallmentsController

**Arquivo**: `controllers/cost_installments_controller.rb`

**Filtros:**
- `before_action :set_cost_installment, except: [ :new, :create ]`
- `before_action :set_cost, except: %i[ show edit_description show_report_row update_description]`
- `before_action :set_report_sum, only: %i[ edit_description show_report_row ]`

**Acoes:**
- `def show`
- `def new`
- `def create`
- `def edit`
- `def update`
- `def requisition`
- `def pay`
- `def undo`
- `def cancel`
- `def edit_description`
- `def show_report_row`
- `def update_description`

---

### CostSpreadsheetsController

**Arquivo**: `controllers/cost_spreadsheets_controller.rb`

**Filtros:**
- `before_action :set_cost_spreadsheet, only: :show`

**Acoes:**
- `def new`
- `def create`
- `def show`

---

### CostsController

**Arquivo**: `controllers/costs_controller.rb`

**Filtros:**
- `before_action :set_cost, except: %i[index new create]`

**Acoes:**
- `def index`
- `def show`
- `def new`
- `def create`
- `def edit`
- `def update`
- `def items`
- `def save_items`
- `def destroy`

---

### ExecutingTeamsController

**Arquivo**: `controllers/executing_teams_controller.rb`

**Filtros:**
- `before_action :set_executing_team`

**Acoes:**
- `def edit`
- `def update`

---

### FinancialAccounts::BalancesController

**Arquivo**: `controllers/financial_accounts/balances_controller.rb`

**Acoes:**
- `def set_balanceable`
- `def set_sortable_table_header_path`

---

### FinancialAccountsController

**Arquivo**: `controllers/financial_accounts_controller.rb`

**Filtros:**
- `before_action :set_financial_account, only: %i[ show edit update destroy ]`

**Acoes:**
- `def index`
- `def show`
- `def new`
- `def edit`
- `def create`
- `def update`
- `def destroy`

---

### FinancialCategoriesController

**Arquivo**: `controllers/financial_categories_controller.rb`

**Filtros:**
- `before_action :set_financial_category, only: %i[edit update destroy show transfer]`
- `before_action :set_root_financial_category, only: %i[new create edit update]`

**Acoes:**
- `def index`
- `def new`
- `def create`
- `def edit`
- `def update`
- `def destroy`
- `def transfer`

---

### HomeController

**Arquivo**: `controllers/home_controller.rb`

**Acoes:**
- `def index`

---

### ImportedCostsController

**Arquivo**: `controllers/imported_costs_controller.rb`

**Filtros:**
- `before_action :set_cost_spreadsheet, only: %i[ new create ]`
- `before_action :set_imported_cost, except: %i[ new create ]`
- `before_action :set_report_sum, only: %i[ edit_description show_report_row ]`

**Acoes:**
- `def new`
- `def create`
- `def show`
- `def edit`
- `def edit_row`
- `def edit_description`
- `def show_report_row`
- `def show_row`
- `def update_description`
- `def update`
- `def undo`

---

### IncomeInstallmentsController

**Arquivo**: `controllers/income_installments_controller.rb`

**Filtros:**
- `before_action :set_income_installment, except: %i[ index new create ]`
- `before_action :set_project, only: %i[ new create show ]`
- `before_action :set_report_sum, only: %i[ edit_description show_report_row ]`

**Acoes:**
- `def index`
- `def show`
- `def new`
- `def create`
- `def edit`
- `def update`
- `def cancel`
- `def requisition`
- `def bill`
- `def receive`
- `def edit_description`
- `def show_report_row`
- `def update_description`

---

### PayrollSpreadsheetsController

**Arquivo**: `controllers/payroll_spreadsheets_controller.rb`

**Filtros:**
- `before_action :set_payroll_spreadsheet, only: %i[show destroy]`

**Acoes:**
- `def index`
- `def show`
- `def new`
- `def create`
- `def destroy`

---

### PayrollsController

**Arquivo**: `controllers/payrolls_controller.rb`

**Filtros:**
- `before_action :default_filters, only: %i[index group subgroup]`

**Acoes:**
- `def index`
- `def group`
- `def subgroup`
- `def show`

---

### PeopleController

**Arquivo**: `controllers/people_controller.rb`

**Filtros:**
- `before_action :set_person, only: %i[show edit update destroy]`

**Acoes:**
- `def index`
- `def show`
- `def new`
- `def edit`
- `def create`
- `def update`
- `def destroy`

---

### PersonTypesController

**Arquivo**: `controllers/person_types_controller.rb`

**Filtros:**
- `before_action :set_person_type, only: %i[edit update destroy show transfer]`
- `before_action :set_root_person_types, only: %i[new edit create update]`

**Acoes:**
- `def index`
- `def new`
- `def create`
- `def edit`
- `def update`
- `def destroy`
- `def transfer`

---

### ProjectBudgetAllocationsController

**Arquivo**: `controllers/project_budget_allocations_controller.rb`

**Filtros:**
- `before_action :set_project_budget_allocation, only: %i[ show destroy summary update export ]`
- `before_action :set_project`

**Acoes:**
- `def new`
- `def create`
- `def show`
- `def summary`
- `def update`
- `def destroy`
- `def export`

---

### ProjectsController

**Arquivo**: `controllers/projects_controller.rb`

**Filtros:**
- `before_action :set_project, only: %i[show edit update destroy]`

**Acoes:**
- `def index`
- `def show`
- `def new`
- `def edit`
- `def create`
- `def update`
- `def destroy`

---

### RelevantFactsController

**Arquivo**: `controllers/relevant_facts_controller.rb`

**Filtros:**
- `before_action :set_project`
- `before_action :set_relevant_fact, only: %i[ edit update destroy ]`

**Acoes:**
- `def new`
- `def create`
- `def edit`
- `def update`
- `def destroy`

---

### ReportController

**Arquivo**: `controllers/report_controller.rb`

**Filtros:**
- `before_action :set_report`

**Acoes:**
- `def index`
- `def totals`

---

### SpreadsheetAttachmentsController

**Arquivo**: `controllers/spreadsheet_attachments_controller.rb`

**Filtros:**
- `before_action :set_spreadshet_attachment, only: %i[ show destroy ]`
- `before_action :set_project`

**Acoes:**
- `def show`
- `def new`
- `def create`
- `def destroy`

---

### TransfersController

**Arquivo**: `controllers/transfers_controller.rb`

**Filtros:**
- `before_action :set_transfer, except: %i[ index new create ]`
- `before_action :set_report_sum, only: %i[ edit_description show_report_row ]`

**Acoes:**
- `def index`
- `def show`
- `def new`
- `def create`
- `def edit`
- `def update`
- `def undo`
- `def edit_description`
- `def show_report_row`
- `def update_description`

---

### UnitiesController

**Arquivo**: `controllers/unities_controller.rb`

**Filtros:**
- `before_action :set_unity, only: %i[show edit update]`

**Acoes:**
- `def index`
- `def show`
- `def new`
- `def create`
- `def edit`
- `def update`

---

### UploadsController

**Arquivo**: `controllers/uploads_controller.rb`

**Filtros:**
- `before_action :set_blob`

**Acoes:**
- `def show`

---

### Users::RegistrationsController

**Arquivo**: `controllers/users/registrations_controller.rb`

**Acoes:**
- `def new`
- `def create`

---

### UsersController

**Arquivo**: `controllers/users_controller.rb`

**Filtros:**
- `before_action :set_user, only: %i[ show edit update lock unlock]`

**Acoes:**
- `def index`
- `def show`
- `def edit`
- `def update`
- `def lock`
- `def unlock`

---

## Services (Logica de Negocio)

### AllCost

**Arquivo**: `services/all_cost.rb`

**Metodos Publicos:**
- `def initialize`
- `def query`
- `def imported_costs`
- `def cost_installments`

---

### AttachmentParser

**Arquivo**: `services/attachment_parser.rb`

**Metodos Publicos:**
- `def initialize`
- `def text`

---

### ChatService

**Arquivo**: `services/chat_service.rb`

**Metodos Publicos:**
- `def initialize`
- `def call`
- `def client`
- `def response`
- `def content`
- `def messages`
- `def message`

---

### CostProcessor

**Arquivo**: `services/cost_processor.rb`

**Metodos Publicos:**
- `def initialize`
- `def xlsx`
- `def costs`
- `def import`

---

### ExpenseAttachmentsValidator

**Arquivo**: `services/expense_attachments_validator.rb`

**Metodos Publicos:**
- `def initialize`
- `def bank_account`
- `def other_cpf_cnpj_in_receipt`
- `def checklist`

---

### ExtractImageService

**Arquivo**: `services/extract_image_service.rb`

**Metodos Publicos:**
- `def initialize`
- `def messages`
- `def prompt`
- `def request`

---

### ExtractService

**Arquivo**: `services/extract_service.rb`

**Metodos Publicos:**
- `def messages`
- `def prompt`
- `def request`

---

### PayrollProcessor

**Arquivo**: `services/payroll_processor.rb`

**Metodos Publicos:**
- `def initialize`
- `def xlsx`
- `def payroll`
- `def payroll_hash`
- `def import`

---

### ProjectParser

**Arquivo**: `services/project_parser.rb`

**Metodos Publicos:**
- `def rubrics`
- `def process`
- `def name`

---

### ReceiptParser

**Arquivo**: `services/receipt_parser.rb`

**Metodos Publicos:**
- `def names`
- `def cpfs`
- `def cnpjs`
- `def values`
- `def sanity_check?`

---

### Report

**Arquivo**: `services/report.rb`

**Metodos Publicos:**
- `def initialize`
- `def resources`
- `def totals`
- `def all`
- `def balance`
- `def financial_account`
- `def cost_center`
- `def size`

---

### SpreadsheetProcessor

**Arquivo**: `services/spreadsheet_processor.rb`

**Metodos Publicos:**
- `def initialize`
- `def xlsx`
- `def equipe_executora`
- `def passagens`
- `def diarias`
- `def outras_rubricas`
- `def equipe_hash`
- `def passagens_hash`
- `def diarias_hash`
- `def outras_rubricas_hash`
- `def import`

---

### ValidateService

**Arquivo**: `services/validate_service.rb`

**Metodos Publicos:**
- `def initialize`
- `def messages`
- `def prompt`
- `def request`

---

### ZipExporter

**Arquivo**: `services/zip_exporter.rb`

**Metodos Publicos:**
- `def initialize`
- `def call`

---

## Jobs (Processamento Assincrono)

### ExtractionJob

**Arquivo**: `jobs/extraction_job.rb`

**Metodos Publicos:**
- `def perform`

**Fila:**
- `:default`

---

### ValidationJob

**Arquivo**: `jobs/validation_job.rb`

**Metodos Publicos:**
- `def perform`

**Fila:**
- `:default`

---

## Indice Tematico

### Gestao de Projetos
- `Project`
- `ProjectBudgetAllocation`
- `RelevantFact`
- `SpreadsheetAttachment`

### Gestao de Custos
- `Cost`
- `CostInstallment`
- `CostAllocation`
- `CostCenter`
- `ImportedCost`
- `Transfer`

### Gestao Financeira
- `FinancialAccount`
- `FinancialCategory`
- `Balance`
- `Bank`

### Gestao de Pessoas e Organizacoes
- `User`
- `Unity`
- `Company`
- `CompanyType`
- `Person`
- `PersonType`
- `ExecutingTeam`

### Gestao Documental
- `Attachment`
- `AttachmentType`
- `CostSpreadsheet`
- `PayrollSpreadsheet`

> Ultima atualizacao automatica: 06/04/2026 13:21:38
