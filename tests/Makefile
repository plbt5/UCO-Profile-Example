#!/usr/bin/make -f

# Portions of this file contributed by NIST are governed by the following
# statement:
#
# This software was developed at the National Institute of Standards
# and Technology by employees of the Federal Government in the course
# of their official duties. Pursuant to title 17 Section 105 of the
# United States Code this software is not subject to copyright
# protection and is in the public domain. NIST assumes no
# responsibility whatsoever for its use by other parties, and makes
# no guarantees, expressed or implied, about its quality,
# reliability, or any other characteristic.
#
# We would appreciate acknowledgement if the software is used.

SHELL := /bin/bash

top_srcdir := $(shell cd .. ; pwd)

RDF_TOOLKIT_JAR := $(top_srcdir)/dependencies/UCO/lib/rdf-toolkit.jar

profile_ttls := $(wildcard $(top_srcdir)/ontology/uco-*.ttl)

shape_ttls := $(wildcard $(top_srcdir)/shapes/sh-*.ttl)

all: \
  monolithic.ttl

.pyshacl.done.log: \
  exemplars.ttl \
  monolithic.ttl
	source $(top_srcdir)/venv/bin/activate \
	  && pyshacl \
	    --ont-graph monolithic.ttl \
	    --shacl monolithic.ttl \
	    exemplars.ttl
	touch $@

.pytest.done.log: \
  $(profile_ttls) \
  exemplars.ttl \
  test_exemplar_coverage.py
	source $(top_srcdir)/venv/bin/activate \
	  && pytest \
	    --log-level=DEBUG
	touch $@

check: \
  .pytest.done.log \
  .pyshacl.done.log

clean:
	@rm -f \
	  .*.done.log \
	  monolithic.ttl

monolithic.ttl: \
  $(RDF_TOOLKIT_JAR) \
  $(top_srcdir)/.venv.done.log \
  $(top_srcdir)/dependencies/imports-transitive.ttl \
  $(profile_ttls) \
  $(shape_ttls)
	$(MAKE) \
	  --directory $(top_srcdir)/dependencies/UCO \
	  .venv.done.log
	$(MAKE) \
	  --directory $(top_srcdir)/dependencies/UCO/tests \
	  uco_monolithic.ttl
	source $(top_srcdir)/venv/bin/activate \
	  && rdfpipe \
	    --output-format turtle \
	    $(top_srcdir)/dependencies/UCO/dependencies/collections-ontology/collections.owl \
	    $(top_srcdir)/dependencies/UCO/dependencies/error/docs/current/error.ttl \
	    $(top_srcdir)/dependencies/UCO/tests/uco_monolithic.ttl \
	    $(top_srcdir)/dependencies/imports-transitive.ttl \
	    $(profile_ttls) \
	    $(shape_ttls) \
	    > _$@
	source $(top_srcdir)/venv/bin/activate \
	  && pyshacl \
	    --shacl $(top_srcdir)/dependencies/UCO/ontology/owl/owl.ttl \
	    _$@
	mv _$@ $@
