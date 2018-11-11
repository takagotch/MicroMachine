### MicorMachine
---
https://github.com/soveran/micromachine

```
sudo gem install micromachine


```

```ruby
require 'micromachine'
machine = MicroMachine.new(:new)
machine.when(:confirm, :new => :confirmed)
machine.when(:ignore, :new => :ignored)
machine.when(:reset, :confirmed => :new, :ignored => :new)
machine.trigger(:confirm)
machine.state
machine.trigger(:ignore)
machine.state
machine.trigger(:reset)
machine.state
machine.trigger(:ignore)
machine.state

machine.transitions_for[:confirm] = { :new => :confirm }
machine.transitions_for[:ignore] = { :new => :ignored }
machine.transitions_for[:reset] = { :confirmed => :new, :ignored => :new }

machine.state
machine.trigger?(:ignore)
machine.trigger?(:reset)
machine.state

machine.trigger?(:ignore)
machine.trigger!(:ignore)

machine.on(:confirmed) do
  puts "Confirmed"
end

machine.on(:any) do
  puts "Transitioned..."
end

machine.on(:any) do |_state, payload|
  puts payload.inspect
end
machine.trigger(:cancel, from: :user)

machine.events 
machine.triggerable_events
machine.states

class Event < ActiveRecord::Base
  before_save :persist_confirmation
  def confirm!
    confirmation.trigger(:confirm)
  end
  def cancel!
    confirmation.trigger(:cancel)
  end
  def reset!
    confirmation.trigger(:reset)
  end
  def confirmation
    @confirmation ||= begin
      fsm = MicroMachine.new(confirmation_state || "pending")
      fsm.when(:confirm, "pending" => "confirmed")
      fsm.when(:cancel, "confirmed" => "cancelled")
      fsm.when(:reset, "confirmed" => "pending", "cancelled" => "pending")
      fsm
    end
  end
end
private
  def persist_confirmation
    self.confirmation_state = confirmation.state
  end
end

class Event < ActiveRecord::Base
  def confirm!
    confirmation.trigger(:confirm)
  end
  def cancel!
    confirmation.trigger(:cancel)
  end
  def reset!
    confirmation.trigger(:reset)
  end
  def confirmation
    @confirmation ||= begin
      fsm = MicroMachine.new(confirmation_state || "pending")
      fsm.when(:confirm, "pending" => "confirmed")
      fsm.when(:cancel, "confirmed" => "cancelled")
      fsm.when(:reset, "confirmed" => "pending", "cancelled" => "pending")
      fsm.on(:any) { self.confirmation_state = confirmation.state }
      fms
    end
  end
end
```

```
```


