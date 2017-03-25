.PHONY: open close

open:
	@python -m SimpleHTTPServer 8000&
	@sleep 1
	@open http://localhost:8000

close:
	@ps -alx | grep "python -m SimpleHTTPServer" | grep -v grep | awk '{print "kill -9", $$2}' | sh
